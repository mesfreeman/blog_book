## 背景

最近在做项目时遇到一个需求，需要将显示在网页上的部分内容生成`pdf`文档供使用者下载并打印出来。我们项目中使用的是PHP语言，因此小编对PHP语言下怎么生成PDF文件做了一个深入的探索，并记录如下，希望对有同样需要的小伙伴有一些帮助。

## 几种常用的方式对比

为了满足项目有需要，同时节约开发成本及提高开发效率，小编对目前市场上php转pdf相关的技术做了一个调查，并从以下几个方面做为参考依据，从而评估出一个最优的方案出来：

* 名字
* 官方网站
* 立项日期
* 最后更新时间
* 是否使用命名空间
* 是否符全psr-4规范(此规范必有composer.json)
* 代码量
* 功能
* 文档情况

### TCPDF

TCPDF是一个开源的PHP类库，项目开始于2002年，据说现在已经被成千上万的人使用。有[官方网站](https://tcpdf.org)和[github](https://github.com/tecnickcom/tc-lib-pdf),并且在持续更新中。

特点：

* 符合`PSR-4`规范，但无命名空间概念
* 不依赖于其它PDF函数库
* 标准页面格式，支持页面格式自定义、边距自定义和单元格
* 支持UTF-8编码和文字方向从右到左语言
* 支持图片和图形转换
* 支持页面压缩
* 支持链接

优点：

* 有更新及维护
* 有详细的说明文档及大量实例供参考

### FPDF

FPDF是一个允许使用纯PHP生成PDF文档的PHP类，换句话说，没有使用PDFlib函数库。FPDF中的F代表免费和自由：你可以在任何情况下使用，并且支持自定义，来满足你特定的需求。同样也有[官方网站](http://www.fpdf.org)和[github](https://github.com/Setasign/FPDF)。

特点：

* 符合`PSR-4`规范，但无命名空间概念
* 标准页面格式，支持页面格式自定义、边距自定义和单元格
* 可以管理页眉及页脚
* 自动换行和文本对齐
* 支持图片
* 支持链接

优点：

* 更新也比较及时
* 文档和例子相对比较完整

### Html2PDF

Html2PDF是收费的，因此小编也没有深入去研究它，如果想了解的可以去它的[官网](http://www.html2pdf.solutions/)see see。

### html2ps

html2ps是一个开源项目，有自己的[官方网站](http://www.rustyparts.com/pdf.php)。

特点：

* 可加密生成的文档
* 标准页面格式，支持页面格式自定义、边距自定义和单元格
* 支持链接

优点：

* 有说明文档

缺点：

* 实例较少
* 更新不及时，在2013.3.16号已停止更新

### HTML_ToPDF

HTML_ToPDF是一个开源项目，有自己的[官方网站](http://www.rustyparts.com/pdf.php)。能很容易的将html页面转换成pdf文档。

特点：

* 自定义头尾样式和颜色
* 设置页面大小和边距
* 支持图片转换
* 支持在html代码中使用css

缺点：

* 实例较少，文档不详细

### Dompdf

Dompdf是`github`上的一个开源项目，但没有官方网站，只有一个[github地址](https://github.com/dompdf/dompdf)。一直在更新维护中，小编感觉比较奇怪的是好多人用为什么没有文档呢，难道都是大神，能无师自通。

## 项目介绍

### 最终方案

经过上面一系列的对比，同时结合项目的情况，小编最终选择了`TCPDF`。并对代码做了一些简化处理，[源码在这里](https://coding.net/u/mesfreeman/p/TCPDF/git)。

原因有三个：

1. 更新和维护及时
2. 例子和说明文档详细
3. 符合`PSR-4`规范

### 方案实施

项目中使用的框架是`ZendFramework 2`，运行环境是`ubuntu`,服务器是`apache`,为了简化小编并没有使用`composer`的方式让`TCPDF`与`ZendFrameWork 2`相结合，而是用了`include`的方式。

步骤：

1.上传`TCPDF`代码到服务器，如下

    double@Jellyfish:/usr/share$ cd convertPdf/
    double@Jellyfish:/usr/share/convertPdf$ ll
    total 16
    drwxr-xr-x   4 root root 4096 Oct 20 16:57 ./
    drwxr-xr-x 131 root root 4096 Oct 20 09:58 ../
    drwxr-xr-x   2 root root 4096 Oct 20 16:57 package/
    lrwxrwxrwx   1 root root   12 Oct 20 10:43 TCPDF -> TCPDF-6.2.13/
    drwxr-xr-x   6 root root 4096 Oct 20 16:46 TCPDF-6.2.13/

2.将它加入到环境变量中

    # 进到phpini中
    sudo vim /etc/php5/apache2/php.ini
    # 找到include_path
    /include_path
    # 设置include_path，以冒号分隔
    include_path = ".:/usr/share/convertPdf/TCPDF"

3.在ZendFramework 2的入口文件处，包含过来

找到init_autoloader.php，并写入下面代码：

    // TCPDF autoload
    require_once 'tcpdf.php';

4.调用测试

    $pdf = new \TCPDF(PDF_PAGE_ORIENTATION, PDF_UNIT, PDF_PAGE_FORMAT, true, 'UTF-8', false);

    // 设置文档信息
    $pdf->SetAuthor('PATOZON');
    $pdf->SetTitle('测试一下');

    // 设置头尾外边距
    $pdf->SetMargins(PDF_MARGIN_LEFT, PDF_MARGIN_TOP, PDF_MARGIN_RIGHT);
    $pdf->SetHeaderMargin(PDF_MARGIN_HEADER);
    $pdf->SetFooterMargin(PDF_MARGIN_FOOTER);

    // 设置字体
    $pdf->SetFont('simyou', '', 10);

    // 添加页码
    $pdf->AddPage();

    // 设置内容
    $html = "<b>测试一下</b>"

    // 生成pdf文件
    $pdf->writeHTMLCell(0, 0, '', '', $html, 0, 1, 0, true, '', true);
    $pdf->Output("测试.pdf", 'I');


## 参考资料

[1] [怎么用PHP在HTML中生成PDF文件](https://segmentfault.com/a/1190000002452512)

[2] [TCPDF官网](https://tcpdf.org)

[3] [php命名空间](http://php.net/manual/zh/language.namespaces.php)

[4] [PSR-4规范](https://segmentfault.com/a/1190000000380008)

[5] [如何使用PHP代码生成PDF文件](http://www.jiandanjimei.com/jiandanjimei.com.php?id=37512955AE979)

[6] [dompdf的使用](http://blog.csdn.net/wide288/article/details/41982423)

[7] [如何设置PHP inclue_path](http://www.cyberciti.biz/faq/how-do-i-set-php-include-path-in-php-ini-file/)

[8] [TCPDF中文字体问题](http://blog.csdn.net/penglonghu/article/details/7799615) 