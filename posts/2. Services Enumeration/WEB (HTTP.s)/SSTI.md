>[!Information]
>模板根据网站的代码框架(php，python，js)提供。渲染代码与用户输入相结合，来提供显示给用户。该漏洞存在与网页的二次渲染时。
>**模板注入可以XSS、LFI的形式出现**
![[Pasted image 20240707192125.png|400]]
模板引擎，协助网页设计师与后端工程师独立开发。
将编程对象填入html页面中。
**决策树**
![[Pasted image 20240707192716.png|400]]

### Detect
纯文本上下文中的注入，通常也会招到XSS攻击，通过常见的模板引擎语句在文本中来找寻注入点 - `{7 * 7*}
```python
#代码上下文中的赋值语句，print语句
#再变量名后添加html<tag>探测注入点
personal_greeting=username}} <tag>
```
### Identify
1. Burpsuite模板注入发现模块
2. 手工注入 -- 根据决策树。
### Exploit
**找到引擎内建的对象(self)或者开发者嵌入的对象**，可以使用爆破进行。
#### Payload
BurpSuite修改HTTP请求包，来加载payload。利用[child_process(Node.js)](https://nodejs.org/api/child_process.html)来`child_process.exec(command[, options][, callback])`
##### SandBox
遇到沙箱： 利用Node.js内建的全局对象`process`中的`process.mainModule`来打开主函数入口，而*主函数通常不会在沙箱环境下*
`"return process.mainModule.require('child_process').exec('<command>');"`or`"process.mainModule.require('child_process').execSync('<command>');"`