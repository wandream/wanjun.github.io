<!--
 * @Author: Mia
 * @Date: 2022-01-24 15:59:38
 * @LastEditors: Mia
 * @LastEditTime: 2022-01-24 16:25:08
 * @Description: 
-->
### 前端上传大文件处理

当在做文件的导入功能的时候，如果导入的文件过大，可能会导致所需要的的时间很长，且失败后需要重新上传

 - **对文件做切片**，将一个请求拆分成多个请求，每个请求的时间就会缩短，且如果某个请求失败，只需要重新发送这一次请求即可，无需从头开始
 - **通知服务器合并切片**，在上传完切片之后，前端通知服务器做合并切片操作
 - **控制多个请求的并发量**，防止多个请求同时发送，造成浏览器内存溢出，导致页面卡死
 - **做断点续传**，当多个请求中有请求发送失败，例如出现网络故障，页面关闭等，得对失败的请求做处理，让其重复发送


1. 切片，合并切片
```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=s, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.bootcdn.net/ajax/libs/axios/0.24.0/axios.min.js"></script>
</head>
<body>
    <input type="file" id="fileInput">
    <button id="uploadBtn">上传</button>
</body>
<script>
// 请求基准地址
axios.defaults.baseURL = 'http://localhost:3000'
// 选中的文件
var file = null
// 选择文件
document.getElementById('fileInput').onchange = function({target: {files}}){
    file = files[0]
}
// 开始上传
document.getElementById('uploadBtn').onclick = async function(){
    if (!file) return
    // 创建切片
    // let size = 1024 * 1024 * 10 //10MB 切片大小
    let size = 1024 * 50  //50KB 切片大小
    let fileChunks = []
    let index = 0 //切片序号
    for(let cur = 0; cur < file.size; cur += size){
        fileChunks.push({
            hash: index++,
            chunk: file.slice(cur, cur + size)
        })
    }
    // 上传切片
    const uploadList = fileChunks.map((item, index) => {
        let formData = new FormData()
        formData.append('filename', file.name)
        formData.append('hash', item.hash)
        formData.append('chunk', item.chunk)
        return axios({
            method: 'post',
            url: '/upload',
            data: formData
        })
    })
    await Promise.all(uploadList)
    // 合并切片
    await axios({
        method: 'get',
        url: '/merge',
        params: {
            filename: file.name
        }
    });
    console.log('上传完成')
}
</script>
</html>
```
