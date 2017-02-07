### 前言

文件上传在各种语言里都会涉及到,属于一项目基本技能。这篇文章将针对PHP的文件上传所涉及到知识做一个总结。

### 前端页面

前端需要一个表单来选取需要上传的文件，并且也可以先在前端对文件大小或文件类型做一层限制，代码如下：

    <html>
        <body>
            <form action="upload_file.php" method="post" enctype="multipart/form-data">
                <input type="file" name="files[]" accept=".xls" multiple="multiple" id="file" onchange="checkinfo(this)"/> 
                <br />
                <input type="submit" name="submit" value="Submit" />
            </form>
            <script>
                function checkinfo(obj) {
                    var len = obj.files.length;
                
                    var text="";
                    for (var i = 0 ; i < len ; i++) {
                        var fileSize = obj.files[i].size;
                        if (fileSize > 1024 * 1024) {
                            alert("文件大小不能超过1M");
                        }
                    }
                }
            </script>
        </body>
    </html>

说明：

* 文件上传属于二进制数据，需要使用`multipart/form-data`
* `input`的`tyepe="file"`属性来指明该输入框作为文件来处理，`accept=".xls"`属性来指明上传文件类型只能是`xls`文件，`mutiple="multiple"`属性说明支持选择多个文件进行上传
* 对于文件大小折判断，属于HTML5新增的特性，不仅可以判断大小，还支持多文件上传、本地图片预览等，详细见`扩展`

扩展：

1.多文件上传及文件大小获取，示例如下：

    <input type="file" multiple="multiple" onchange="checkinfo(this)">
    <script>
    function checkinfo(obj){
        var len = obj.files.length;
    
        for (var i = 0 ; i < len ; i ++){
            text += "文件:" + obj.files[i].name + " ,大小:" + obj.files[i].size + "字节\n";
        }
        console.log(text);
    }
    </script>

2.图片本地预览，示例如下：

    <input type="file" multiple="multiple" onchange="checkinfo(this)">
    <div id="win"></div>
    <script>
    function checkinfo(obj){
        var len = obj.files.length;
        for (var i = 0; i < len; i ++){
            showimg(obj.files[i]);
        }
    }
    
    function showimg(img){
        var a = new FileReader();
        a.readAsDataURL(img);
        a.onload = function(){
            var img = new Image();
            img.src = a.result;
            document.getElementById('win').appendChild(img);
        }
    }
    </script>

### 后端处理

后端要对前端上传过来的文件根据实际的需求对文件进行相关的处理，其中也包括对文件名、文件类型、文件大小等合法性的检测。

通用方法：

    /**
     * 处理单个上传文件
     * 注：代码有使用了部分ZF2框架，请自行替换
     *
     * @param array $upload 上传文件的数据
     *    结构： array(
     *              'name'     => 'xxx.xx',
     *              'type'     => 'xxx',
     *              'tmp_name' => 'xxx/xxx',
     *              'error'    => 0,
     *              'size'     => 85504,
     *          );
     * @return bool
     */
    protected function processUploadFile(array $upload)
    {
        if (! isset($upload['error'])) {
            throw new Exception\InvalidArgumentException('文件没有被上传');
        }

        // 检查文件是否上传成功
        if ($upload['error']) {
            switch ($upload['error']) {
                case UPLOAD_ERR_INI_SIZE:
                    throw new Exception\RuntimeException('文件大小超过系统限制');
                    break;
                case UPLOAD_ERR_FORM_SIZE:
                    throw new Exception\InvalidArgumentException('文件大小超过表单限制');
                    break;
                case UPLOAD_ERR_PARTIAL:
                    throw new Exception\RuntimeException('文件只有部分被上传');
                    break;
                case UPLOAD_ERR_NO_FILE:
                    throw new Exception\RuntimeException('文件没有被上传');
                    break;
                case UPLOAD_ERR_NO_TMP_DIR:
                    throw new Exception\RuntimeException('找不到临时文件夹');
                    break;
                case UPLOAD_ERR_CANT_WRITE:
                    throw new Exception\RuntimeException('文件写入失败');
                    break;
                default:
                    throw new Exception\RuntimeException('文件上传失败');
                    break;
            }
        }

        // 检查文件大小
        $maxFileSize = 10 * 1024 * 1024; // 单个文件不能大于10M
        if (! isset($upload['size']) || $upload['size'] > $maxFileSize) {
            throw new Exception\InvalidArgumentException('文件大小不能超过10M');
        }

        // 检查文件名
        $basename = basename($upload['name']);
        // 防止XSS攻击
        $basename = str_replace(array("'", '"', '<', '>', '&'), ' ', $basename);

        if (! in_array($upload['type'], array('application/vnd.ms-excel'), true)) {
            throw new Exception\InvalidArgumentException('只能上传xls文件');
        }

        // 保存文件
        $realPath = ROOT_PATH . self::UPLOAD_DIR . "/{$reportedDate}/{$basename}";
        $filePath = "{$realPath}.tmp"; // 临时文件，业务处理成功后转为正式文件

        // 创建文件存放路径
        $dir = dirname($realPath);
        if (! file_exists($dir)) {
            @mkdir($dir, 0755, true);
        }

        if (file_exists($realPath)) {
            throw new Exception\RuntimeException('文件已上传，请不要重得上传');
        }

        if (! move_uploaded_file($upload['tmp_name'], $filePath)) {
            throw new Exception\RuntimeException('文件保存失败');
        }

        // 业务处理（对文件内容的相关操作，如果没有可以忽略该过程）

        // 重命名文件
        if (! rename($filePath, $realPath)) {
            throw new Exception\RuntimeException('文件上成功，但文件归档失败');
        }

        return true;
    }

调用示例：

    // 接收文件
    $filesArray = $_FILES['files'];
    // 数据转换
    $uploadArray = array();
    for ($i = 0; $i < count($filesArray['name']); $i ++) {
        $uploadArray[] = array(
            'name'     => $filesArray['name'][$i],
            'type'     => $filesArray['type'][$i],
            'tmp_name' => $filesArray['tmp_name'][$i],
            'error'    => $filesArray['error'][$i],
            'size'     => $filesArray['size'][$i],
        ); 
    }
    // 调用文件处理函数
    foreach ($uploadArray as $upload) {
        try {
            $result = $this->processUploadFile($upload);
            // 业务逻辑处理
        } catch () {
            $message = $e->getMessage();
            // 记录日志
        }
    }

### 结语

至此，文件上传就搞定了。不过文件上传是一个很危险的操作，不但要在前端做检查和限制，更要在后端做好安全防范，防止不法用户向服务器传送非法文件，来危及服务器安全。上面示例中后台仅仅对文件的`type`做了一个小小判断，在实际应用了这样子使用危险还是很大的，切记！！！