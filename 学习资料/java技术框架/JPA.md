我期望的结果为：

where article0_.article_mode=? and (article0_.article_title like ? or article0_.article_summary like ? or article0_.article_content like ?）

百度了一下and和or如何组合查询的解决方法，感觉比较复杂，如果通过直接写query的方法，在要支持分页的时候比较麻烦，查到通过jpa本身来解决的方法比较复杂，根据输出的SQL我想到了一种比较简单但是不是特别优雅的方法，方法名，测试代码及输出结果如下：

findDistinctByArticleModeAndArticleTitleContainingOrArticleModeAndArticleSummaryContainingOrArticleModeAndArticleContentContaining(int mode,String titleKey,int mode1,String summaryKey,int mode2,String contentKey);

生成的查询条件：where article0_.article_mode=? and (article0_.article_title like ?) or article0_.article_mode=? and (article0_.article_summary like ?) or article0_.article_mode=? and (article0_.article_content like ?)