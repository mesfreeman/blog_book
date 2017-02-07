### Data URI

当你考虑图片流与Data URI之间的互转时，想必你对Data URI是什么已经有了充分的了解，在这里就不做叙述了。如果想进一步去了解，请走传送门[Data URI详解](http://www.ljiong.com/?p=419)

### 图片转化为Data URI

要想知道图片是怎么转为`Data URI`？就必须要了解`Data URI`是有哪些部分组成的

示例：

    data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACYAAAAoCAYAAACSN4jeAAAE8klEQVRYCe2X33MTVRTHP+ymCWlJCTRAaFNKpQid0lJ+1ArTyi/DIFPHBx3U0Qff9Q/w1VeefWbGH6OO4zBl7EgVgToyMCOihaq0WoRAqAHamhIy6abJ6tzN7maT7pasMCMPvQ/Zc8/9nnO+Offcs3eXSG1d//AEDukJ5KRRWiTmdmcWM7aYMbcZcIv3VGbgpWdnB/V+mWzyGv0jk5WZPQKqMmK+jTzX1Y4PUBOZ/5OYl33dHaySIS/+tTeEbPz76npe7vZiKmSZfDJG/0gCAh28++YO/CqoqIaF+ZSQQMpw8fPPGJgy1baCQ8aqae1sZ41NM5Fq19KxfW2Zsw3kbnzCAIjQ2o9GogxVmNo4tcE5EIPZTA7VD6oKHo8FpqrkhFLE93gKRACPF8iKTBVG6tYYo4kMHpHqfB5veANtjUF99eEPS0QrOMmxDz8yFT0HXiH6dEBUGH988ykfX89qa4deeo1d9X7IJbkltkZA9JGKX2ZgOG1MaWpf44pYBXkN0dZkRExze6JAyowohLzKgxIFyB5/iWapbFZpid5p8lBiO7qfoV4cR0BJXOOsUnSV104G2kEQO2kd+VzGOuVvE1yidpw4bGUB39S6l77ta3TjND99+7PFUZDGlTrjuSz3LStCXN7cxZFgtnB483n8kfoyxMJTR2I7du6nr6vJLO57l88wmN3EO29spWpOgeogQX8h4crMJDE9jrEFNeFm2sL2wR2DWuC2mOgLR+hZX2PClMQw75+fhPB66mprkCiuiQNx85drOjZPVlWR9VNrOjAFCSmfZLK8IM31orDE7mq9unkbbx3q1MKnJ4Y5eqK4hX0Ho6zzyyCJTjXL2IUznEoUHT4uyZaY5jwQpmd5inPxNPsOHKYrXI1Knrm5nDiE5pCrPFRJ2sHUdLKkNS5zvfiKUBj/7mv64zan2oI2RNut1BZTCc6lCjDfsiA1tXqhG5aunwEiDbUQr+wC4EysJLCRIpV0MolitAn9FTSnzqEiIzZXNqpfs1fx14UwOppqsStxbzOpkJhhqXDl+AkGLb1MrER7ewmRIZWMMyBe5paxujXK23sjFk1loktiEkuXiU5rdR7kqc0t1AtP6mpiI18xYlle4XPX8Q3TksQbyoWeufJFX4iA/veUu/ESUuVQN3PXxOY5XxYwayg3W5LKeVA3ikcm1tO5HqMeZv664yb2gtgKiRVhBomC1zCdLcYdK8OfV5MQCNJk6SyzlvBai7PMFxJL4zgglQcpMmlRXRnSlv4YPdjDKp2zOjXOzXW9vPd8i3ZvS09N8PvVEfqHBzlJBw3VXqZj5a96h4CAc+d3tgFfiNcP72dzuPjOHB38EnnPi2w0mpZhr2aYGPuN8xeuMOKiBCsk5mVTeCXrIhEaGxtpCAfNuhLx0zd+4OjJX8VXC8+2t9HeupFIXZG0wTGVuM6lC+c5m7Ck3VgsezoSa2rt5dXuBiSpCp+veLcvtVeIXTzDsR9Lm6qG8YXo27WNLZsi6Lcj0zR9b5zTX3zPJVMzX3CssVh8Gmlvi9kKrKaqkiYRG2Xo9BXGrAtWWZlkYOgUA0PiU3A3XR3N1OjRaupWUmXF2siOGRPY6IHDbFkBM/dTzMxMc+fuFPcmpxlLPXwr5sfysm/3HnZtDXN76DgfXC1+qMzH/tfit/P0mHXFBvWYHT+qu0VibjP4xGbsXziQXSO2RqocAAAAAElFTkSuQmCC
    
说明:

从上面可以看出Data URI是由三部分组成的：

1. image/png：表示数据呈现的格式
2. base64：表明其数据编码的方式
3. xxxx：根据上述编码形成的数据

**那么图片怎么转化为Data URI呢？**

其实很简单只需要拼出这三个部分就可以了，如：

    data:image/png; + base64, + base64_encode(图片数据流) 
    
#### 代码示例

    // 读取出图片流，用curl的方式更好一点
    $imgData = file_get_contens('http://blog.hequanxi.com/usr/themes/jianshu-master/img/touxiang.jpg');
    // 拼接出Data URI
    echo 'data:image/png;base64,' . base64_encode($imgData);
    
搞定，就这么简单~

### Data URI转化为图片

知道了`图片转怎么化为Data URI`的，那么Data URI转化为图片就迎刃而解了。

#### 代码示例

    $imgDataUri = 'xxxx'; // 好长的一段Data URI，比如说上面的
    // 截取base64编码的数据
    $base64Data = substr($str, 22);
    // 对数据进行解码
    $imgData = base64_decode($base64Data);
    // 写入文件
    file_put_contents('dataUri.png', $imgData);

搞定，也是这么简单~