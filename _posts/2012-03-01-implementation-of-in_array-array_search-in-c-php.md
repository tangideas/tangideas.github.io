---
title: PHP in_array()与array_search的实现
author: James Tang
layout: post
permalink: /code/implementation-of-in_array-array_search-in-c-php/
categories:
  - Code
  - Notes
tags:
  - C
  - PHP
---
今天看PHP源码的时候发现in\_array与array\_search是通过同一个函数php\_search\_array来实现的，只是最后一个参数不同. 

in_array的实现：

<pre class="brush:c">PHP_FUNCTION(in_array)
{
    php_search_array(INTERNAL_FUNCTION_PARAM_PASSTHRU, 0);
}
</pre>

array_search的实现：

<pre class="brush:c">PHP_FUNCTION(array_search)
{
    php_search_array(INTERNAL_FUNCTION_PARAM_PASSTHRU, 1);
}
</pre>

php\_search\_array的代码：

<pre class="brush:c">static void php_search_array(INTERNAL_FUNCTION_PARAMETERS, int behavior)
{
    zval *value,                /* value to check for */
         *array,                /* array to check in */
         **entry,               /* pointer to array entry */
          res;                  /* comparison result */
    HashPosition pos;           /* hash iterator */
    zend_bool strict = 0;       /* strict comparison or not */
    ulong num_key;
    uint str_key_len;
    char *string_key;
    int (*is_equal_func)(zval *, zval *, zval * TSRMLS_DC) = is_equal_function;

    if (zend_parse_parameters(ZEND_NUM_ARGS() TSRMLS_CC, "za|b", &#038;value, &#038;array, &#038;strict) == FAILURE) {
        return;
    }

    if (strict) {
        is_equal_func = is_identical_function;
    }

    zend_hash_internal_pointer_reset_ex(Z_ARRVAL_P(array), &#038;pos);
    while (zend_hash_get_current_data_ex(Z_ARRVAL_P(array), (void **)&#038;entry, &#038;pos) == SUCCESS) {
        is_equal_func(&#038;res, value, *entry TSRMLS_CC);
        if (Z_LVAL(res)) {
            if (behavior == 0) {
                RETURN_TRUE;
            } else {
                /* Return current key */
                switch (zend_hash_get_current_key_ex(Z_ARRVAL_P(array), &#038;string_key, &#038;str_key_len, &#038;num_key, 0, &#038;pos)) {
                    case HASH_KEY_IS_STRING:
                        RETURN_STRINGL(string_key, str_key_len - 1, 1);
                        break;
                    case HASH_KEY_IS_LONG:
                        RETURN_LONG(num_key);
                        break;
break;
                }
            }
        }
        zend_hash_move_forward_ex(Z_ARRVAL_P(array), &#038;pos);
    }

    RETURN_FALSE;
}
</pre>

behavior参数为1时为search，0为判断元素是否存在.  
默认情况下用is\_equal\_function函数来判断元素值是否相同，如果是严格比较，则使用is\_identical\_function函数。

in\_array与array\_search在PHP中调用时的参数都是( mixed $needle , array $haystack [, bool $strict = false ] ), 所以  
zend\_parse\_parameters的type_spec参数的值是&#8221;za|b&#8221;, z表示任意类型, a表示必须是array, |表示之后的参数为可选, b是boolean。
