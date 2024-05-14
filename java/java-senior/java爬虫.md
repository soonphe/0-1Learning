# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java爬虫——webmagic

### java爬虫介绍
先声明，语言并不是局限，不必纠结选择java还是python去做，会哪个哪个好，你会java，就用java写的好，会Python，就用Python写的好，难道还为了写爬虫而特地学一门语言？
java爬虫库：
1. Apache Nutch
```
    （1）官方网站：http://nutch.apache.org/
（2）是否支持分布式：是
（3）可扩展性：中。Apache Nutch并不是一个可扩展性很强的爬虫，它是一个专门为搜索引擎定制的网络爬虫，虽然Apache Nutch具有一套强大的插件机制，但通过定制插件并不能修改爬虫的遍历算法、去重算法和爬取流程。
（4）适用性：Apache Nutch是为搜索引擎定制的爬虫，具有一套适合搜索引擎的URL维护机制（包括URL去重、网页更新等），但这套机制并不适合目前大多数的精抽取业务（即结构化数据采集）。
（5）上手难易度：难。需要使用者熟悉网络爬虫原理、hadoop开发基础及linux shell，且需要熟悉Apache Ant
```
2. WebCollector
```
（1）官方网站：https://github.com/CrawlScript/WebCollector
（2）可扩展性：强
（3）适用性：WebCollector适用于精抽取业务。
（4）上手难易度：简单
```
3. WebMagic：推荐
```
官方网站http://webmagic.io/
（1）官方网站：https://github.com/code4craft/webmagic
（2）是否支持分布式：支持
（3）可扩展性：强
（4）适用性：WebMagic适用于精抽取业务。
（5）上手难易度：简单。
```
4. Crawler4j
```
（1）官方网站：https://github.com/yasserg/crawler4j
（2） 是否支持分布式：否
（3）可扩展性：低。Crawler4j实际上是一个单机版的垂直爬虫，其遍历算法是一种类似泛爬的算法，虽然可以添加一些限制，但仍不能满足目前大部分的精抽取业务。另外，Crawler4j并没有提供定制http请求的接口，因此Crawler4j并不适用于需要定制http请求的爬取业务（例如模拟登陆、多代理切换）。
（4）上手难易度：简单
```

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