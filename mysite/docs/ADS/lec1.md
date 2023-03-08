###  AVL Trees, Splay Trees,and Amortized Analysis

---

- **AVL树(*Adelson-Velskii-Landis*)**:任何节点的左右子树高度差不超过1

- AVL树是一种特殊的BST树

- ![image-20230228104432711](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202302281044874.png)
  - Nov:troublemaker, Mar:troublefinder

> 求深度为D的AVL树的最少节点数:

- 高度=深度+1
- empty tree 的深度为-1
- 用f(d)代表深度为d的AVL树的最少节点数,易知:`f(-1)=0,f(0)=1`,有递归公式:$f(d) = f(d-1)+f(d-2)+1$

---

- [二叉平衡树AVL平衡调整数据结构](https://www.bilibili.com/video/BV1xE411h7dd/?vd_source=fc348f083480294816113d924016802c)

- [平衡二叉搜索树（AVL树）代码实现](https://www.bilibili.com/video/BV1NV411E7DQ/?spm_id_from=333.788.recommend_more_video.3&vd_source=fc348f083480294816113d924016802c)
