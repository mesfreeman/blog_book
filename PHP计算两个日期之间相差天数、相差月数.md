### 思路

* 计算相差天数：将两个日期都先转换成时间戳，二者相减取绝对值除以`86400`
* 计算相差月数：将两个日期以分隔符进行拆分，年份相减，月份相减，然后计算月数

### 代码示例

#### 计算相差天数

    /**
     * 获取两个日期相差天数
     *
     * 例如：“2015-08-01” 至 “2016-09-01” 相差多少天
     *
     * @param string $startTime  开始时间
     * @param string $endTime    结束时间
     * @return int               相差天数
     */
    function getDayCount($startTime, $endTime)
    {
        $startTime = strtotime($startTime);
        $endTime = strtotime($endTime);
    
        return abs($endTime - $startTime) / 86400;
    }

#### 计算相差月数

    /**
     * 获取两个日期相差月份
     *
     * 例如：“2015-08-01” 至 “2016-09-01” 相差多少月
     *
     * @param string $startTime  开始时间
     * @param string $endTime    结束时间
     * @param string $delimiter  年月之间的分隔符，如“-”、“/”
     * @return int               相差月数
     */
    function getMonthCount($startTime, $endTime, $delimiter)
    {
        if (strtotime($startTime) > strtotime($endTime)) {
            throw new Exception('开始日期不能大于结束日期');
        }
        
        $startTimeArr = explode($delimiter, $startTime);
        $endTimeArr = explode($delimiter, $endTime);
    
        return ($endTimeArr[0] - $startTimeArr[0]) * 12 + ($endTimeArr[1] - $startTimeArr[1]);
    }

### 拓展

在函数`strtotime()`的转换过程中，意外发现了一个大坑，记录如下：

    echo date('Ym', strtotime('2016-08'));   // 201608
    echo date('Ym', strtotime('201608'));    // 201609
    echo date('Ym', strtotime('20160801'));  // 201608

由上例可以看出，在时间戳的转换过程中，如果时间只有年月且无分隔符时，转换出来的时间戳**不准确**，但当时间有年月日或者年月之间有分隔符时转换成时间戳时是**准确的**。