### 站点名称

    $this->options->title();

### 站点网址

    $this->options->siteUrl();

### 完整路径标题

    $this->archiveTitle(' &raquo; ', < span class="string">'', ' | '); ?><?php $this ->options->title();

### 站点说明

    $this->options->description();

### 模板文件夹地址

    $this->options->themeUrl();

### 导入模板文件夹内的php文件

    $this< /span>->need('.php');

### 文章或者页面的作者

    $this->author();

### 作者头像

    $this->author->gravatar('40');

### 该文作者全部文章列表链接

    $this->author->permalink ();

### 该文作者个人主页链接

    $this->author->url();

### 该文作者的邮箱地址

    $this->author->mail();

### 上一篇与下一篇调用代码

    $this->thePrev();
    $this->theNext();

### 判断是否为首页，输出相关内容

    <?php if ($this->is('index')): ?>
        //首页输出内容
    <?php else: ?>
        //不是首页输出内容
    <?php endif; ?>

### 文章或页面，评论数目

    $this->commentsNum('No Comments', '1 Comment' , '%d Comments');

### 截取部份文章（首页每篇文章显示摘要），350是字数

    $this->excerpt(350, '...');

### 调用自定义字段

    $this->fields->fieldName;

### RSS地址

    $this->options->feedUrl();

### 获取最新post

    $this->widget('Widget_Contents_Post_Recent', 'pageSize=8&type=category')->parse('<li><a href="{permalink}">{title}</a></li>');

### 纯文字分类名称，不带链接

    $this->category(',', false);

### 获取文章分类列表

    <ul>
        <?php $this->widget('Widget_Metas_Category_List')->parse('<li><a href="{permalink}">{name}</a> ({count})</li>'); ?>
    </ul>

### 获取某分类post

    <ul>
        <?php $this->widget('Widget_Archive@indexyc', 'pageSize=8&type=category', 'mid=1')->parse('<li><a href="{permalink}" title="{title}">{title}</a></li>'); ?>
    </ul>

### 获取最新评论列表

    <ul>
        <?php $this->widget('Widget_Comments_Recent')->to($comments); ?>
        <?php while($comments->next()): ?>
            <li><a href="<?php $comments->permalink(); ?>"><?php $comments->author(false); ?></a>: <?php $comments->excerpt(50, '...'); ?></li>
        <?php endwhile; ?>
    </ul>

### 首页获取最新文章代码限制条数

    <?php while ($this->next()): ?>
        <?php if ($this->sequence <= 3): ?>
            html
        <?php endif; ?>
    <?php endwhile; ?>

### 获取最新评论列表第二个版本，只显示访客评论不显示博主也就是作者或者说自己发的评论

    <?php $this->widget('Widget_Comments_Recent','ignoreAuthor=true')->to($comments); ?>
        <?php while($comments->next()): ?>
        <li><a href="<?php $comments->permalink(); ?>"><?php $comments->author(false); ?></a>: <?php $comments->excerpt(50, '...'); ?></li>
    <?php endwhile; ?>

### 获取文章时间归档

    <ul>
        <?php $this->widget('Widget_Contents_Post_Date', 'type=month&format=F Y')->parse('<li><a href="{permalink}">{date}</a></li>'); ?>
    </ul>

### 获取标签云

    <?php $this->widget('Widget_Metas_Tag_Cloud', 'ignoreZeroCount=1&limit=28')->to($tags); ?>
    <?php while($tags->next()): ?>
        <a href="<?php $tags->permalink(); ?>" class="size-<?php $tags->split(5, 10, 20, 30); ?>"><?php $tags->name(); ?></a>
    <?php endwhile; ?>

### 调用该文相关文章列表

    <?php $this->related(5)->to($relatedPosts); ?>
    <?php if ($relatedPosts->have()): ?>
    <?php while ($relatedPosts->next()): ?>
        <li><a href="<?php $relatedPosts->permalink(); ?>" title="<?php $relatedPosts->title(); ?>"><?php $relatedPosts->title(); ?></a></li>
    <?php endwhile; ?>
    <?php else : ?>
        <li>无相关文章</li>
    <?php endif; ?>

### 隐藏head区域的程序版本和模版名称

    $this->header("generator=&template=");

### 获取读者墙

    $period = time() - 999592000; // 时段: 30 天, 单位: 秒
    $counts = Typecho_Db::get()->fetchAll(Typecho_Db::get()
        ->select('COUNT(author) AS cnt','author', 'url', 'mail')
        ->from('table.comments')
        ->where('created > ?', $period )
        ->where('status = ?', 'approved')
        ->where('type = ?', 'comment')
        ->where('authorId = ?', '0')
        ->group('author')
        ->order('cnt', Typecho_Db::SORT_DESC)
        ->limit(25));
    $mostactive = '';
    $avatar_path = 'http://www.gravatar.com/avatar/';
    foreach ($counts as $count) {
        $avatar = $avatar_path . md5(strtolower($count['mail'])) . '.jpg';
        $c_url = $count['url']; if ( !$c_url ) $c_url = Helper::options()->siteUrl;
        $mostactive .= "<a href='" . $c_url . "' title='" . $count['author'] . " (参与" . $count['cnt'] . "次互动)' target='_blank'><img src='" . $avatar . "' alt='" . $count['author'] . "的头像' class='avatar' width='32' height='32' /></a>\n";
    }
    echo $mostactive;


### 登陆与未登录用户展示不同内容

    <?php if($this->user->hasLogin()): ?>
        登陆可见
    <?php else: ?>
        未登录和登陆均可见
    <?php endif; ?>

### 导航页面列表调用隐藏特定的页面 这个演示隐藏了album和search两个页面

    <ul>
        <li<?php if($this->is('index')): ?> class="current"<?php endif; ?>><a href="<?php $this->options->siteUrl(); ?>">主页</a></li>
        <?php $this->widget('Widget_Contents_Page_List')->to($pages); ?>
        <?php while($pages->next()): ?>
            <?php if (($pages->slug != 'album') && ($pages->slug != 'search')): ?>
                <li<?php if($this->is('page', $pages->slug)): ?> class="current"<?php endif; ?>><a href="<?php $pages->permalink(); ?>" title="<?php $pages->title(); ?>"><?php $pages->title(); ?></a></li>
            <?php endif; ?>
        <?php endwhile; ?>
    </ul>

### 文章参考

[1] [王爵博客][1]
[2] [枫林博客][2]


  [1]: https://biezhi.me/2015/09/19/share-some-of-the-commonly-used-typecho-function/#section
  [2]: http://www.blogfeng.com/typecho.html