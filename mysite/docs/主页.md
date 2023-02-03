## 怎样部署到Github

------

###  从头开始  

1. 新建文件夹  

2. 用**vscode**打开新文件夹  

3. 在**vscode**里新建终端  

4. 依次输入：

   ```
   py -m venv venv  
   venv\Scripts\activate   
   ```

   设置虚拟环境并且激活虚拟环境*(目的是与系统环境分开)*  

5. ```
   pip install mkdocs-material  
   ```

   (只有第一次要做)  

6. ```
   mkdocs new mysite  
   ```

    *(这里的mysite可以是任何名字)*  

7. ```
   cd mysite  
   ```

8. ```
   mkdocs serve  
   ```

   *(这时候已经打开了一个网页，随着你vscode里的改动，网页会实时刷新改变)*  

9. 接下来则是根据[Mkdocs使用说明](https://squidfunk.github.io/mkdocs-material/getting-started/)简单地在`mkdocs.yml`中简单地设置即可，这时，一个新的项目就建好了

### 维护网页

1. 使用**typora**做好笔记  

2. 将笔记复制到`root`文件夹下对应的目录中，编号  

3. 在`mkdocs.yml`中进行设置，格式如下：  

   ```
   nav:  
     - 主页:  
       - index.md  
     - 概率论与数理统计:  
       - 第一章: 概率论与数理统计/20230202.md  
       - 第二章: ...  
   ```

4. 输入代码如下：

   ```
   git init  
   git add .  
   git commit -m "update"//可以不叫update
   git remote add origin https://github.com/Tianjiangyigeyi/note.git  
   git push -u origin master  
   ```

5. 进入`mysite`文件夹  

   ```
   cd mysite  
   ```

6. 输入代码如下：  

   ```
   mkdocs gh-deploy  
   ```

7. 等待30s左右，网页已成功更新.https://tianjiangyigeyi.github.io/note/  

## 可能用到的网站

----

- [Mkdocs使用说明](https://squidfunk.github.io/mkdocs-material/getting-started/)  
- [Markdown官方教程](https://markdown.com.cn/cheat-sheet.html#%E6%80%BB%E8%A7%88)  

- [Latex数学公式语法](https://blog.csdn.net/yen_csdn/article/details/79966985)
