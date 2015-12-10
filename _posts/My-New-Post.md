title: My New Post
date: 2015-12-07 19:01:42
tags:
- php
---
## this is My New Post
```
<?php
public function indexAction(){
    $pageSize = 10;
    $page = $this->getRequest()->getQuery("page") ? $this->getRequest()->getQuery("page") : 1;
    //$page = $this->getRequest()->getParam("page") ? $this->getRequest()->getParam("page") : 1;
    $article_list = $this->m_article->getArticlesList($page, $pageSize, 'normal');
    foreach ($article_list as $key => $row) {
        $article_list[$key]['date'] = date('Y-m-d H:i:s',$row['create_ts']);
    }
    $total_num = $this->m_article -> getArticleTotal('normal');

    $pageObj = new Tool_Pagination();
    $page_html = $pageObj->markPhpPager('?page={page}',$page,$pageSize,$total_num);

    $title = "文章列表";
    $this->getView()->assign('page_string', $page_html);
    $this->getView()->assign('title', $title);
    $this->getView()->assign('data_list', $article_list);
    $this->getView()->display('./article/index.html');
    exit;
}
```
