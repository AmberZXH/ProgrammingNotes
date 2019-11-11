<font size=6>**PHP 常见排序算法**</font>

常见排序算法实例，未完待续...
----

原始数组： `$arrs = [1, 6, 5, 9, 10, 2, 8, 7, 4, 3];`

# 冒泡排序

	// 冒泡排序
	/**
	 * 冒泡排序
	 *
	 * @param  array  $arr  所需排序的数组
	 * @param  string  $sort  排序规则
	 *
	 * @return array 排序后的新数组
	 */
	function BubbleSort(array $arr, $sort = 'asc')
	{
	    $length = count($arr);
	    if ($length <= 1) {
	        return $arr;
	    }
	    if ($sort == 'desc') {
	        for ($i = 0; $i < $length; $i++) {
	            for ($j = $length - 1; $j > $i; $j--) {
	                if ($arr[$j - 1] < $arr[$j]) {
	                    $temp = $arr[$j];
	                    $arr[$j] = $arr[$j - 1];
	                    $arr[$j - 1] = $temp;
	                }
	            }
	        }
	
	        return $arr;
	    }
	    if ($sort == 'asc') {
	        for ($i = 0; $i < $length; $i++) {
	            for ($j = $length - 1; $j > $i; $j--) {
	                if ($arr[$j] < $arr[$j - 1]) {
	                    $temp = $arr[$j];
	                    $arr[$j] = $arr[$j - 1];
	                    $arr[$j - 1] = $temp;
	                }
	            }
	        }
	
	        return $arr;
	    }
	}
	
	print_r(BubbleSort($arrs));


# 快速排序

	/**
	 * 快速排序
	 *
	 * @param  array  $arr  所需排序的数组
	 * @param  string  $sort  排序规则
	 *
	 * @return array 排序后的新数组
	 */
	function QSort(array $arr, $sort = 'asc')
	{
	    $length = count($arr);
	    if ($length <= 1) {
	        return $arr;
	    }
	    $povit = $arr[0]; // 枢轴
	    $left_arr = [];
	    $rigth_arr = [];
	
	    if ($sort == 'asc') {
	        for ($i = 1; $i < $length; $i++) { //注意 $i 从1开始
	            if ($arr[$i] <= $povit) {
	                $left_arr[] = $arr[$i];
	            } else {
	                $rigth_arr[] = $arr[$i];
	            }
	        }
	        $left_arr = QSort($left_arr);  // 递归排序左半部分
	        $rigth_arr = QSort($rigth_arr);  // 递归排序右半部分
	    }
	
	    if ($sort == 'desc') {
	        for ($i = 1; $i < $length; $i++) { //注意 $i 从1开始
	            if ($arr[$i] >= $povit) {
	                $left_arr[] = $arr[$i];
	            } else {
	                $rigth_arr[] = $arr[$i];
	            }
	        }
	        $left_arr = QSort($left_arr, 'desc');  // 递归排序左半部分
	        $rigth_arr = QSort($rigth_arr, 'desc');  // 递归排序右半部分
	    }
	
	    return array_merge($left_arr, [$povit], $rigth_arr);  // 合并左半部分，枢轴，右半部分
	}
	
	print_r(QSort($arrs, 'desc'));

# 选择排序

	/**
	 * 选择排序
	 *
	 * @param  array  $arr  所需排序的数组
	 * @param  string  $sort  排序规则
	 *
	 * @return aray  排序后的新数组
	 */
	function SelectSort(array $arr, $sort = 'asc')
	{
	    $length = count($arr);
	    if ($length < 1) {
	        return $arr;
	    }
	    static $result = [];
	    if ($sort == 'asc') {
	        $pivot = min($arr);
	        foreach ($arr as $k => $v) {
	            if ($v == $pivot) {
	                $result[] = $v;
	                unset($arr[$k]);
	            }
	        }
	        SelectSort($arr);
	    }
	    if ($sort == 'desc') {
	        $pivot = max($arr);
	        foreach ($arr as $k => $v) {
	            if ($v == $pivot) {
	                $result[] = $v;
	                unset($arr[$k]);
	            }
	        }
	        SelectSort($arr,'desc');
	    }
	    return $result;
	
	}
	
	print_r(SelectSort($arrs,'desc'));


# 插入排序

	/**
	 * 插入排序
	 *
	 * @param  array  $arr  所需排序的数组
	 *
	 * @return array  排序后的新数组
	 */
	function InsertSort(array $arr)
	{
	    $length = count($arr);
	    if ($length <= 1) {
	        return $arr;
	    }
	    for ($i = 1; $i < $length; $i++) {
	        $x = $arr[$i];
	        $j = $i-1;
	        while ($x < $arr[$j] && $j >= 0) {
	            $arr[$j+1] = $arr[$j];
	            $j--;
	        }
	        $arr[$j+1] = $x;
	    }
	    return $arr;
	}
	
	print_r(InsertSort($arrs));


