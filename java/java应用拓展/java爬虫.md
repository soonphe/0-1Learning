# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## java爬虫——webmagic
### 相关依赖
<dependency>
    <groupId>us.codecraft</groupId>
    <artifactId>webmagic-core</artifactId>
    <version>0.7.3</version>
</dependency>
<dependency>
    <groupId>us.codecraft</groupId>
    <artifactId>webmagic-extension</artifactId>
    <version>0.7.3</version>
</dependency>

### 技术介绍
1.Downloader
Downloader负责从互联网上下载页面，以便后续处理。WebMagic默认使用了Apache HttpClient作为下载工具。
2.PageProcessor
PageProcessor负责解析页面，抽取有用信息，以及发现新的链接。WebMagic使用Jsoup作为HTML解析工具
3.Scheduler
Scheduler负责管理待抓取的URL，以及一些去重的工作
4.Pipeline
Pipeline负责抽取结果的处理，

### webmagic采用的页面抽取技术：
1.Xpath：用于XML中获取元素的一种查询语言，也可以用于html
 page.getHtml().xpath("//h1[@class='entry-title public']/strong/a/text()")
：查找所有class属性为'entry-title public'的h1元素，并找到他的strong子节点的a子节点，并提取a节点的文本信息
2.Css选择器
3.正则表达式
4.JsonPath：与Xpath类似，用于json中快速定位一条内容



1.webmagic
配置代理：
  HttpClientDownloader httpClientDownloader = new HttpClientDownloader();
    httpClientDownloader.setProxyProvider(SimpleProxyProvider.from(
new Proxy("101.101.101.101",8888,"username","password")	//这个可以配置多个IP代理池
));
    spider.setDownloader(httpClientDownloader);

1.博客地址：	http://blog.sina.com.cn/flashsword20	http://blog\\.sina\\.com\\.cn/s/blog_\\w+\\.html
2.博文列表地址：	http://blog.sina.com.cn/s/articlelist_1487828712_0_1.html
3.博文详情地址：	http://blog.sina.com.cn/s/blog_58ae76e80100to5q.html

page.addTargetRequests(page.getHtml().xpath("//div[@class=\"articleList\"]")	//选中所有区域，这里应为 blognavInfo
.links()	//获取所有链接
.regex("http://blog\\.sina\\.com\\.cn/s/blog_\\w+\\.html").all());	//url过滤，去掉编辑，更多等链接


### 编码
~~~~
public class GithubRepoPageProcessor implements PageProcessor {

    //部分一：抓取网站的相关配置，包括编码，抓取间隔，重试次数
    private Site site = Site.me().setCharset("utf-8").setRetryTimes(3).setSleepTime(1000);

    /**
     * 爬虫逻辑核心，编写抽离逻辑
     * @param page
     */
    @Override
    public void process(Page page) {

        //部分二：定义如何抽取页面信息并保存
        //获取html中所有满足正则表达式regex（）的链接
        page.addTargetRequests(page.getHtml().links().regex("(https://github\\.com/\\w+/\\w+)").all());
        page.putField("author", page.getUrl().regex("https://github\\.com/(\\w+)/.*").toString());
        //xpath：用于xml/html获取元素
        page.putField("name", page.getHtml().xpath("//h1[@class='public']/strong/a/text()").toString());
        if (page.getResultItems().get("name")==null){
            //skip this page
            page.setSkip(true);
        }
        page.putField("readme", page.getHtml().xpath("//div[@id='readme']/tidyText()"));
    }

    @Override
    public Site getSite() {
        return site;
    }

    public static void main(String[] args) {
        //Spider是爬虫启动的入口
        Spider.create(new GithubRepoPageProcessor())
                //从哪个站点开始抓取
                .addUrl("https://github.com/soonphe")
                //webmagic中用于保存结果的组建Pipeline——使用json保存：JsonFilePipeline
                .addPipeline(new JsonFilePipeline("D:\\Logs\\webmagic\\"))
                //开启多少线程
                .thread(5)
                //启动爬虫
                .run();
    }
}
~~~~