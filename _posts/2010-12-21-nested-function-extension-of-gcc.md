---
title: Nested function extension of gcc
author: James Tang
layout: post
permalink: /notes/nested-function-extension-of-gcc/
categories:
  - Notes
tags:
  - C
  - gcc
---
[gcc][1] supports Nested functions as the following example shows:

<pre class="brush:c">void isort (int arr[], int len) {

        //shift_element() is the nested function
	void shift_element (int i) {
		int ivalue;
		for (ivalue=arr[i]; i &#038;&#038; arr[i-1] > ivalue; i--) {
			arr[i] = arr[i-1];
		}

		arr[i] = ivalue;
	}

	int i;
	for (i=1; i &lt; len; i++) {
		if (arr[i] &lt; arr[i-1]) {
			shift_element(i);
		}
	}
}
</pre>

gcc also provides many other useful features which can NOT be found in ANSI standard c. For more information, refer to [Extensions to the C Language Family][2].

 [1]: http://gcc.gnu.org/
 [2]: http://gcc.gnu.org/onlinedocs/gcc-2.95.3/gcc_4.html