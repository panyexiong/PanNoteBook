# Requst

## body的四种参数

### Form-data

`multipart/form-data`是默认用来转换网络表单为数据的请求体类型。它模拟了在一个网站上填写一个表单，然后进行提交。form-data 编辑器允许你设置 键值对( key-value) (对你的数据使用 data editor）。它同样允许你为multi-part表单请求的每一部分都表明内容类型。你也可以将一个文件加载到一个key上面。

- multipart/form-data将表单中的每个input转为了一个由boundary分割的小格式，没有转码，直接将utf8字节拼接到请求体中，在本地有多少字节实际就发送多少字节，极大提高了效率，适合传输长字节。

### x-www-form-Urlencoded

- 它是post的默认格式，使用js中的Urlencoded转码方式，将name、value中的空格替换为加号；将非ASCII字符做百分号编码；将input中的name、value用=连接，不同的input用&连接。
- 同样使用Urlencoded转码，post和get区别在于，get把转换、拼接完成的字符串用？直接与表单的acton连接作为URL的一部分，所以请求体里面没有数据；而post把转换、拼接后的字符串放在请求体里面，不会在浏览器的地址栏显示，因而更安全一些。

### Raw

raw(原始) 请求可以包含任何类型的数据。

### Binary