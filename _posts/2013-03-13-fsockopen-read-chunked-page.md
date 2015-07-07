---
title: Use fsockopen to read chunked page
author: James Tang
layout: post
permalink: /code/fsockopen-read-chunked-page/
categories:
  - Code
tags:
  - PHP
---
PHP provides many different ways to download a page(file) through HTTP, the simplest way is using file\_get\_contents function which is suitable for relatively small files. If try to download large file with file\_get\_contents the PHP allowed memory (configured by memory_limit directive) may be exhausted with a fatal error. What&#8217;s why we need a portable way to deal with large file, where fsockopen comes in.

There are two main conditions we should consider: Content-Lenght specified or Chunked data.

## 0. Initiate socket connection

Use fsockopen() to initiate the socket connection. You&#8217;d better specify the error number, error message and timeout parameters, and process the error if exists.

<pre class="brush: php">$url = 'http://test.example.com/fetch_file.php?file=testfile.iso';

if (preg_match_all('#http://([^/]+)(/.+)#i', $url, $matches)) {
    $host = $matches[1][0];
    $path = $matches[2][0];
} else {
    die('Invalid URl');
}

$fp = fsockopen($host, 80, $errno, $error, 30);

//Open a file pointer for write
$wfp = fopen('file-write-to', 'w');

//specify the block size to read
$readBlockSize = 512;
</pre>

## 1. Content-Length

If the Content-Length is specified by HTTP response header, the reading is straightforward just as reading general files.

Snippet use to read response body:

<pre class="brush: php">$data = fread($fp, $readBlockSize);
fwrite($wfp, $data);
</pre>

## 2. Chunked

For chuncked encoding, there is a different data format, here is a quotation from WikiPedia: 

> Each chunk starts with the number of octets of the data it embeds expressed in ASCII followed by optional parameters (chunk extension) and a terminating CRLF sequence, followed by the chunk data. The chunk is terminated by CRLF. If chunk extensions are provided, the chunk size is terminated by a semicolon followed with the extension name and an optional equal sign and value.
> 
> The last-chunk is a regular chunk, with the exception that its length is zero. 

The encoded data looks like this:

<pre class="brush:plain">4
Wiki
5
pedia
E
 in

chunks.
0
</pre>

So we have to address the response chunk by chunk. Snippet to do so:

<pre class="brush: php">if ($chunk_length === false) {
    $data = trim(fgets($fp, 128));
    $chunk_length = hexdec($data);
} else if ($chunk_length > 0) {
    $read_length = $chunk_length > $readBlockSize ? $readBlockSize : $chunk_length;
    $chunk_length -= $read_length;
    $data = fread($fp, $read_length);
    fwrite($wfp, $data);
    if ($chunk_length &lt;= 0) {
        fseek($fp, 2, SEEK_CUR);
        $chunk_length = false;
    }
} else {
     break;
}
</pre>

The full script:

<pre class="brush: php; collapse:true"><?php
/**
 * Download file with fsockopen
 * 
 * @see http://tangobean.com
 * @author James Tang<fwsous@gmail.com>
 * @copyright (C) 2013 James Tang.
 */

set_time_limit(600);
ignore_user_abort(true);

//$url = 'http://test.example.com/fetch_file.php?file=testfile.iso';
//$saveToFile = 'tmp.iso';
$url = 'http://test.example.com/fetch_file.php?file=tmp.gz';
$saveToFile = 'tmp.gz';

if (preg_match_all('#http://([^/]+)(/.+)#i', $url, $matches)) {
    $host = $matches[1][0];
    $path = $matches[2][0];
} else {
    die('Invalid URl');
}

$fp = fsockopen($host, 80, $errno, $error, 30);
$readBlockSize = 512;

if ($fp) {

    $wfp = fopen($saveToFile, 'w');

    if ($wfp) {
        $request = "GET $path HTTP/1.1\r\n";
        $request .= "Host: $host\r\n";
        $request .= "Connection: close\r\n";
        $request .= "User-Agent: php-download/1.0\r\n";
        $request .= "\r\n";

        fwrite($fp, $request);

        $body_start = false;
        $md5sum = '';
        $content_length = false;
        $chunk_length = false;

        $startLine = fgets($fp, 128);

        if ($startLine &#038;&#038; preg_match('#^HTTP/1.\d?\s+200\s+#', $startLine)) {
            while (!feof($fp)) {
                if (!$body_start) {
                    $header = fgets($fp, 128);
                    echo $header;
                    $colon_pos = strpos($header, ':');
                    $header_name = strtolower(trim(substr($header, 0, $colon_pos)));
                    $header_value = trim(substr($header, $colon_pos+1)); 
                    if ($header_name == 'content-md5') {
                        $md5sum = bin2hex(base64_decode($header_value));
                    } else if ($header_name == 'content-length') {
                        $content_length = (int) $header_value;
                    }
                    if ($header == "\r\n") {
                        $body_start = true;
                        echo "Reading data...\n";
                    }
                } else {

                    if ($content_length !== false &#038;&#038; $content_length > 0) {
                        $data = fread($fp, $readBlockSize);
                        fwrite($wfp, $data);
                    } else {
                        if ($chunk_length === false) {
                            $data = trim(fgets($fp, 128));
                            $chunk_length = hexdec($data);
                        } else if ($chunk_length > 0) {
                            $read_length = $chunk_length > $readBlockSize ? $readBlockSize : $chunk_length;
                            $chunk_length -= $read_length;
                            $data = fread($fp, $read_length);
                            fwrite($wfp, $data);
                            if ($chunk_length &lt;= 0) {
                                fseek($fp, 2, SEEK_CUR);
                                $chunk_length = false;
                            }
                        } else {
                            break;
                        }
                    }
                }
            }
        } else {
            echo "Failed to read data: " . $startLine . "\n";
        }

        fclose($wfp);
        if ($md5sum &#038;&#038; strlen($md5sum) > 0) {
            $md5sum_check = bin2hex(md5_file($saveToFile, true));
            if ($md5sum_check != $md5sum) {
                echo 'MD5 checksum does not match: ' . $md5sum_check . "\n";
            } else {
                echo "MD5 checksum match\n";
            }
        } else {
            echo "No MD5 checksum detected\n";
        }
        //unlink($saveToFile);
    }

    fclose($fp);
} else {
    echo 'Error: ' . $errno . '#' . $error . "

<br />\n";
}
</pre>

## 3. Problems

The $readBlockSize value is critical, if too large it may cause problem. When I test on remote server with $readBlockSize=4096, the downloaded file was not identical to source file. This problem must be caused by transfer rate: when you try to read 4096 bytes from the response body, but if less than 4096 bytes was prepared, then the reading sequence is disrupted. At last I found 512 works fine for me.

## 4. Reference

1. [http://en.wikipedia.org/wiki/Chunked\_transfer\_encoding][1]

2. <http://tools.ietf.org/html/rfc2616#page-118>

 [1]: http://en.wikipedia.org/wiki/Chunked_transfer_encoding