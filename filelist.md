#### 图片压缩上传过程中用到的API

　　上周五，在我们前端组例会上，我们的组长做了一个关于图片压缩上传的简介，在上传图片的过程中使用到了一些类，这些看着很眼熟的类，但是真的让你介绍它都有哪些属性，哪些方法时，我相信我是讲不清楚的，所以，我根据上传图片的流程，然后逐一的分析一下这些类。具体的流程，如图所示：

![03](C:\Users\dell\Desktop\20170605\03.png)

##### FileList

 　　FileList来自于一个input元素的files属性，我们可以通过FileList对象访问到用户所选择的文件。所以我们可以通过如下代码来获取到FileList类型的对象。

``` html
<input id="fileItem" type="file">
```

```javascript
 var fileInput = document.querySelector("#fileItem");
 var filelist = null;
 fileInput.onchange = function () {
    filelist = fileInput.files;
    console.log(filelist);
  }
```

　　然后把filelist打印出来，如图所示：

![01](C:\Users\dell\Desktop\20170605\01.png)

　　从上图，对象中有两个File对象和一个length属性。length属性的值为File的个数。那么我们在日常使用的时候就可以通过这个length的值来遍历filelist。遍历的目的，就是取出每个File对象。此时我们item函数就要派上用场了。

　　item函数，根据指定的索引值，FileList对象中指定的File对象。 参数 index 索引值。 返回值 File 所请求的File对象。

　　接下来，让我们用一个例子来体验一下FileList的属性及方法。有一个需求，页面上有一个input框，选中多个文件之后，分别打印出选中的文件。代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<input id="fileItem" multiple type="file">
</body>
<script>
  var fileInput = document.querySelector("#fileItem");
  var filelist = null;
  fileInput.onchange = function () {
    filelist = fileInput.files;
    for (var i = 0, len = filelist.length; i < len; i++) {
      var file = filelist.item(i);
      console.log(file);
    }
  }
</script>
</html>
```

　　打印的结果如下：

![02](C:\Users\dell\Desktop\20170605\02.png)

　　如上图所示，我们把三个文件都打印出来了，但是这个File又是什么类呢？请继续往下看。

##### File

　　上面FileList的介绍中，我们已经把File对象给打印出来了，他有以下属性。

* lastModified : 最后修改时间，时间戳
* lastModifiedDate：最后修改时间，Data对象
* name：文件的名字
* size：文件的大小
* type：文件的MIME类型

　　File类并没有提供任何的方法，但是我们从打印出来file对象可以发现File类继承与Blob类，所以它也一样可以使用Blob类的方法，这里暂且先不提，接着往下看。

##### FileReader

　　从字面的意思上，我们可以理解它是读取文件的一个类。但是需要注意的一点是，它读取文件是异步读取的，它读取文件时需要通过File对象或者Blob对象指定文件。

　　我们可以通过以下代码创建一个FileReader类的对象

```javascript
 var reader = new FileReader()
```

　　我们在使用FileReader类读取文件时，它有三个状态常量，分别是

* EMPTY：空的，没有读取任何文件。
* LOADING：正在加载，数据正在加载中。
* DONE：已完成，已经读取完所有的数据。

　　除了以上三个状态常量，它还有以下三个属性：

* error:  在读取文件时发生的错误。
* readyState: 表示当前FileReader的当前状态，值为上面三个状态常量的其中一个。
* result： 读取到的数据，它的数据格式取决于读取的方法。

　　上面提到的result属性，它的数据格式取决于读取的方法，那么它读取的方法都有哪些呢？我们先来看在这次图片上传中用到的方法，readAsDataURL。

　　readAsDataURL：开始读取指定的File对象或者Blob对象，当操作完成时，上文中readyState的值为DONE，并且会触发onload事件，并且result的值包含一个data：URL格式字符串的值，表示读取文件的内容。看着MDN上面的解释，感觉不到什么，此时应有案例。请看如下代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
  <img id="uploadPreview" style="width: 100px; height: 100px;"/>
  <input id="uploadImage" type="file"/>
</body>
<script type="text/javascript">
  // 获取到input对象
  var fileInput = document.querySelector("#uploadImage");
  var file = null;
  // 初始化一个FileReader对象的实例
  var reader = new FileReader();
  // FileReader对象的读取方法是异步的，所以在onload事件中加载读取到的图片
  reader.onload = function (oFREvent) {
    // 打印读到的内容
    console.log(oFREvent.target.result);
    // 将读取到的内容，绑定在Img标签的src上，用来显示读取到的图片
    document.getElementById("uploadPreview").src = oFREvent.target.result;
  };
  fileInput.onchange = function () {
    // 获取到文件
    file = fileInput.files.item(0);
    // 读取文件
    reader.readAsDataURL(file);
  }
</script>
</html>
```

　　上面的代码不难，并且注释写的很清楚，大家自己看看也能看明白，如果看不明白，也可以自己运行代码看看情况。运行结果如下图所示：

![04](C:\Users\dell\Desktop\20170605\04.png)

　　从图中我们可以验证以上解释，我把读取到的result，赋值给img标签的src，就能正常的显示图片。这里面需要注意的一点是，data后面跟的是文件类型，再后面为数据格式base64，再后面才是真正数据。

　　接下来我们再来看看FileReader的其它的方法：

​	abort方法，看到方法名大家可能都已经猜到它是干什么的，那么恭喜你，猜对了。它就是负责终止某一读取文件的操作，abort执行完成之后，上文中提到的readyState值为DONE。但是有一个需要注意的地方，当没有读取文件的时候，调用了abort函数，会报异常DOM_FILE_ABORT_ERR。

　　readAsArrayBuffer方法，它和上文中提到的readAsDataURL方法一样，也是读取File对象中的内容，不同的是，它的返回的result为ArrayBuffer对象，对象中有一个byteLength的属性，其值为文件的大小，如图所示：

![05](C:\Users\dell\Desktop\20170605\05.png)

　　readAsBinaryString方法，同样的，它也是读取File对象中的内容，result属性中将包含所读取文件的原始二进制数据，一堆乱码，看也看不懂。如图所示：

![06](C:\Users\dell\Desktop\20170605\06.png)

　　readAsText方法，同样的，它也是读取File对象中的内容，result属性性中将包含一个字符串以表示所读取的文件内容。以上几个读取函数的用法和上文中的Demo相似，只需要把方法名替换即可，至于在实际的开发中需要调用哪个方法，那么就得结合你们的使用场景来调用。

##### Blob

　　在写Blob类之前，本来是应该写Image和Canvas的，Image大家都很熟悉，所有的img标签均来自Image对象，img标签是Image类的实例化，大家特别熟悉，就不想再写了。Canvas呢，东西又很多，简单的介绍一下属性和方法，对读者来说并没有什么卵用，所以我想有机会的话，我会专门的写一篇关于Canvas的博客。

　　好了，言归正传，我们在上传之前，需要把压缩完成的base64的图片数据转化为Blob类型的数据，那么我们来看看Blob类。在上文提到，File类继承与Blob类，那么我们就可以认为Blob也是存放文件数据的。那么有人就会想了，我既然有Blob类了，为什么还要File类呢？MDN上说是，Blob表示的数据不一定是一个JavaScript原生格式，所以才会有File类。

　　官方为我们提供了三个只读的属性，分别为：

* isClosed: 表示是否调用过close方法，其实也可以理解这个对象是否已关闭。
* size：这个大家都很清楚，代表包含数据的大小。
* type：字符串，代表对象包含文件的MIME类型。如果类型不明确，则该值为空。[MIME类型](http://www.w3school.com.cn/media/media_mimeref.asp)

　　官方为我们提供了两个方法，分别为：

* close ：关闭 Blob对象，以便能释放底层资源
* slice：返回一个新的Blob对象，包含了源Blob对象中指定范围的数据。

　　在实际的使用中，我们通常使用Blob的构造函数创建一个Blob对象，如下代码所示：

```javascript
var blob = new Blob([JSON.stringify({hello: 'world'}, null, 2)],{type : 'application/json'});
```

　　它的语法如下：var aBlob = new Blob( array, options ); 其中array 是一个由ArrayBuffer、ArrayBufferView、DOMString等对象构成的数组。options是Blob熟悉的json对象，有两个值：type默认值为''，它代表了将会被放入到blob中的数组内容的MIME类型。endings：代表包含结束符/n的字符串如何输出，默认值为transparent，表示保持Blob中保存的结束符不变。它还有另外一个值，native，表示行结束符会被更改为适合宿主操作系统文件系统的惯例。一般使用的时候endings不设置。

##### FormData

　　当我们把压缩好的数据转换为Blob之后，那么最重要的一个类来了，FormData。想必大家都对这个类很熟悉，但是也有很多同学也仅仅是熟悉而已。反正我对它的认知是仅仅是认识而已。那么接下来让我们一起来看看这个类。

　　我们可以通过JavaScript用键值对来模拟表单控件，也可以通过XMLHttpRequst的send方法来提交这个表单。它有一个最大的优点为可以异步上传二进制文件，这也是我们上传文件为什么要用到它的原因。

　　构造函数：`new FormData (form? : HTMLFormElement)`，参数：from，一个html的表单元素，可以包含任何形式的表单控件，一旦指定了from，创建的formdata对象会自动的把表单值也包含进去，包含文件内容。来看个例子体验一把。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form id="myForm" name="myForm">
    <div>
        <label for="username">Enter name:</label>
        <input type="text" id="username" name="username">
    </div>
    <div>
        <label for="useracc">Enter account number:</label>
        <input type="text" id="useracc" name="useracc">
    </div>
    <div>
        <label for="userfile">Upload file:</label>
        <input type="file" id="userfile" name="userfile">
    </div>
</form>
<button id="click">点我</button>
</body>
<script>
 var btnClick = document.querySelector('#click');
 btnClick.onclick =function () {
   // 创建一个含有表单数据的FormData对象
   var formData = new FormData(document.forms.namedItem("myForm"));
  for(var pair of formData.entries()) {
    // 依次打印FormData对象的
     console.log(pair[0], pair[1]);
   }
 }
</script>
</html>
```

　　上面一个简单的demo，创建了一个form表单的FormData对象，然后依次打印出它的键和值，打印的结果如下图所示：

![07](C:\Users\dell\Desktop\20170605\07.png)

　　看了上图，是不是对FormData对象有了新的认识？只是里面的某些方法不知道什么意思罢了，那么现在我们来认识一下FormData对象的方法。

　　append方法，会添加一个新值到 FormData对象内的一个已存在的键中，如果键不存在则会添加该键。语法：`formData.append(name, value, filename);`  其中name， 必填，代表表单名称 ； value，必填， 代表表单的值；filename ，可选，代表传给服务器的文件名称，Blob对象的默认文件名是 "blob"。 File对象的默认文件名是该文件的名称。看下面的demo

```javascript
 var formData = new FormData();
  formData.append('username', 'Mzp');
  console.log(formData.get('username'));  // 打印出  Mzp
```

　　delete方法，会从FormData对象中删除一个指定的key和其对应的值。语法：`formData.delete(name);`没有返回值。这个很简单，也很好理解，就不再写demo了。
　　entries方法，返回一个对象，此对象可以便利访问FormData中的键值对，没有参数。这个方法我们在构造函数中的demo使用过，再看下面的demo加深一下印象。

```javascript
var formData = new FormData();
formData.append('key1', 'value1');
formData.append('key2', 'value2');
// 把键 值对打印出来
for(var pair of formData.entries()) {
   console.log(pair[0], pair[1]); 
}
// 会输出 key1，value1
      // key2, vaule2  
```

　　get方法，返回FormData对象中和指定的键关联的第一个值。语法：`formData.get(name)`，参数：name，将要获取值的键名。但是如果想要获取这个键对应的所有值，可以使用getAll方法，用法和get方法一样，返回值为所有value的集合。请看下面demo

```javascript
  var formData = new FormData();
  formData.append('username', 'Mzp');
  formData.append('username', 'Mzp11');
  console.log(formData.get('username'));  // 打印出  Mzp
  console.log(formData.getAll('username'));  // 打印出  ['Mzp', ''Mzp11]
```

 　　讲到这个地方，需要补充一点，append是如果没有key，则创建一个，如果有key，则把这次的value添加到已有集合的后面。可能有的人就有疑问了，如果我想覆盖掉怎么办？有想法很好，我们可以使用set()方法。它的用法和append一样，唯一的区别就是如果有key，set是会覆盖的，请看下面demo

```javascript
  var formData = new FormData();
  formData.append('username', 'Mzp');
  formData.set('username', 'Mzp11');
  console.log(formData.get('username'));  // 打印出  Mzp11
  console.log(formData.getAll('username'));  // 打印出  ['Mzp11']
```

　　has方法，返回一个布尔值，表示该formdata对象是否包含指定的key。语法：`formData.has(name)`,参数，name，key的名字。由于很好理解，就不再写demo了。

　　keys和values函数，返回一个所有key或者values的集合，可以用来遍历，没有参数，直接调用即可。demo如下：

```javascript
var formData = new FormData();
formData.append('key1', 'value1');
formData.append('key2', 'value2');

// 输出所有的 key
for (var key of formData.keys()) {
   console.log(key); 
}

// 输出所有的value
for (var value of formData.values()) {
   console.log(value); 
} 
```

　　FormData其实介绍到这里已经差不多了，但是为了让大家更好的理解FormData上传文件，找了一个相关的Demo供大家看一下，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form enctype="multipart/form-data" method="post" name="fileinfo">
    <label>Your email address:</label>
    <input type="email" autocomplete="on" autofocus name="userid" placeholder="email" required size="32" maxlength="64" /><br />
    <label>Custom file label:</label>
    <input type="text" name="filelabel" size="12" maxlength="32" /><br />
    <label>File to stash:</label>
    <input type="file" name="file" required />
    <input type="submit" value="Stash the file!" />
</form>
<div></div>
</body>
<script>
  var form = document.forms.namedItem("fileinfo");

  form.addEventListener('submit', function(ev) {
    var outInfo = document.querySelector("div"),
      formData = new FormData(form);

    formData.append("CustomField", "This is some extra data");

    // ajax 请求四部曲 1 创建xhr对象
    var xhr = new XMLHttpRequest();
    var url = '';
    // 2 打开
    xhr.open("POST", url, true);

    /*
    * 3.监听事件，onload 完成加载，
    * onreadystatechange 也可以 因为readyState代表xhr的状态，
    * 0 未打开，可以理解上面四部曲里面的创建对象
    * 1 未发送 可以理解为调用了open
    * 2 获取请求头  send方法已经调用了
    * 3 正在下载响应体
    * 4 请求完成 readystate === 4  ===  onload
    * */
    xhr.onload = function(oEvent) {
      if (xhr.status == 200) {
        outInfo.innerHTML = "Uploaded!";
      } else {
        outInfo.innerHTML = "Error " + xhr.status + " occurred when trying to upload your file.<br \/>";
      }
    };
    // 4. 发送
    xhr.send(formData);
    // 清除默认事件
    ev.preventDefault();
  }, false);
</script>
</html>
```

　　以上是图片压缩上传的过程中使用到的API，我感觉一般的工程师都可以把压缩上传的代码给写出来了，当然，也不排除一些伸手党，那么我就偷偷的把我们组长写好的代码放在下面,伸手党可以直接拿来用，当然，最好还是自己看完上面的文章之后自己写写。

```javascript
export default {
    data() {
      return {
        compressAfter: ''
      }
    },
    methods:{
      inputChange(event){
        var files = event.target.files,
          file = files[0]

        this.promiseFR(file)
          .then((base64Data)=>{
          return this.createImg(base64Data)
        })
      .then((img)=>{
          this.compressAfter = this.drawCanvas(img)
        var blob = this.base2Blob(this.compressAfter)
        this.upload(blob)
      })

      },
      // 获取reader对象 base64
      promiseFR(file){
        var reader = new FileReader()

        return new Promise((resolve,reject)=>{
          // console.log(this)
          reader.onload = function(event) {
          console.log(event.target)
          var base64Data = event.target.result
          resolve(base64Data)
        }
        reader.readAsDataURL(file)
      })
      },
      // 创建img base64 => Image
      createImg(base64Data){
        var img = new Image()

        return new Promise((resolve,reject)=>{

          img.onload = function () {
          var img = this
          resolve(img)

        }
        img.setAttribute('crossOrigin', 'anonymous')
        img.src = base64Data
      })
      },
      // canvas 压缩 Image => base64
      drawCanvas(img){

        var width = img.width,
          height = img.height,
          canvas = document.createElement('canvas'),
          ctx = canvas.getContext('2d')

        canvas.width = width
        canvas.height = height
        ctx.fillStyle = "#fff"
        ctx.fillRect(0, 0, width, height)
        ctx.drawImage(img, 0, 0, width, height)
        // console.log(canvas.toDataURL('image/jpeg', 0.1))
        return canvas.toDataURL('image/jpeg', 0.1)

      },
      // base64 => Blob
      base2Blob(base64){
        var ab = window.atob(base64.split(',')[1])
        var buffer = new ArrayBuffer(ab.length)
        var u8array = new Uint8Array(buffer)

        for(var i=0;i<ab.length;i++){
          u8array[i] = ab.charCodeAt(i)
        }

        var blob = new Blob([u8array.buffer],{type:'image/jpeg'})

        return blob

      },
      // formData 上传
      upload(file){
        var formdata = new FormData()

        formdata.append('fileName',file)
        this.$http.post('http://localhost:5000/upload',formdata)
          .then((res)=>{
          console.log('res : ',res)
      },(rej)=>{
          console.log('rej : ',rej)
        })
      }
    }
  }
```

　　本文中所有的代码，请戳[代码](https://github.com/Mazhp/fileUpload.git)