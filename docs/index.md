---
hide:
  #- navigation # 显示右
  #- toc #显示左
  - footer
  - feedback
comments: false
---
<!--
██╗    ██╗ ██████╗ ██████╗ ██╗    ██╗██╗███╗   ██╗
██║    ██║██╔════╝██╔═══██╗██║    ██║██║████╗  ██║
██║ █╗ ██║██║     ██║   ██║██║ █╗ ██║██║██╔██╗ ██║
██║███╗██║██║     ██║   ██║██║███╗██║██║██║╚██╗██║
╚███╔███╔╝╚██████╗╚██████╔╝╚███╔███╔╝██║██║ ╚████║
 ╚══╝╚══╝  ╚═════╝ ╚═════╝  ╚══╝╚══╝ ╚═╝╚═╝  ╚═══╝
                                                  


____    __    ____  ______   ______   ____    __    ____  __  .__   __. 
\   \  /  \  /   / /      | /  __  \  \   \  /  \  /   / |  | |  \ |  | 
 \   \/    \/   / |  ,----'|  |  |  |  \   \/    \/   /  |  | |   \|  | 
  \            /  |  |     |  |  |  |   \            /   |  | |  . `  | 
   \    /\    /   |  `----.|  `--'  |    \    /\    /    |  | |  |\   | 
    \__/  \__/     \______| \______/      \__/  \__/     |__| |__| \__| 

 __        __                _       
 \ \      / /__ _____      _(_)_ __  
  \ \ /\ / / __/ _ \ \ /\ / / | '_ \ 
   \ V  V / (_| (_) \ V  V /| | | | |
    \_/\_/ \___\___/ \_/\_/ |_|_| |_|
                                     
 ___       ___     ____     ____     ___       ___    _____      __      _  
(  (       )  )   / ___)   / __ \   (  (       )  )  (_   _)    /  \    / ) 
 \  \  _  /  /   / /      / /  \ \   \  \  _  /  /     | |     / /\ \  / /  
  \  \/ \/  /   ( (      ( ()  () )   \  \/ \/  /      | |     ) ) ) ) ) )  
   )   _   (    ( (      ( ()  () )    )   _   (       | |    ( ( ( ( ( (   
   \  ( )  /     \ \___   \ \__/ /     \  ( )  /      _| |__  / /  \ \/ /   
    \_/ \_/       \____)   \____/       \_/ \_/      /_____( (_/    \__/    
                                                                            


-->
                                   

<center><font  color= #518FC1 size=6 class="ml3">hhhjyz的个人主页</font></center>
<script src="https://cdnjs.cloudflare.com/ajax/libs/animejs/2.0.2/anime.min.js"></script>



<center>
<font  color= #608DBD size=3>
<span id="jinrishici-sentence">hello</span>
<script src="https://sdk.jinrishici.com/v2/browser/jinrishici.js" charset="utf-8"></script>
</font>
</center>


<!-- 可选一言 -->
<!-- <center>
<font  color= #608DBD size=3>
<p id="hitokoto">
  <a href="#" id="hitokoto_text" target="_blank"></a>
</p>
<script>
  fetch('https://v1.hitokoto.cn')
    .then(response => response.json())
    .then(data => {
      const hitokoto = document.querySelector('#hitokoto_text')
      hitokoto.href = `https://hitokoto.cn/?uuid=${data.uuid}`
      hitokoto.innerText = data.hitokoto
    })
    .catch(console.error)
</script>
</font>
</center> -->


<div id="rcorners2" >
  <div id="rcorners1">
    <!-- <i class="fa fa-calendar" style="font-size:100"></i> -->
    <body>
      <font color="#4351AF">
        <p class="p1"></p>
<script defer>
    //格式：2020年04月12日 10:20:00 星期二
    function format(newDate) {
        var day = newDate.getDay();
        var y = newDate.getFullYear();
        var m =
            newDate.getMonth() + 1 < 10
                ? "0" + (newDate.getMonth() + 1)
                : newDate.getMonth() + 1;
        var d =
            newDate.getDate() < 10 ? "0" + newDate.getDate() : newDate.getDate();
        var h =
            newDate.getHours() < 10 ? "0" + newDate.getHours() : newDate.getHours();
        var min =
            newDate.getMinutes() < 10
                ? "0" + newDate.getMinutes()
                : newDate.getMinutes();
        var s =
            newDate.getSeconds() < 10
                ? "0" + newDate.getSeconds()
                : newDate.getSeconds();
        var dict = {
            1: "一",
            2: "二",
            3: "三",
            4: "四",
            5: "五",
            6: "六",
            0: "天",
        };
        //var week=["日","一","二","三","四","五","六"]
        return (
            y +
            "年" +
            m +
            "月" +
            d +
            "日" +
            " " +
            h +
            ":" +
            min +
            ":" +
            s +
            " 星期" +
            dict[day]
        );
    }
    var timerId = setInterval(function () {
        var newDate = new Date();
        var p1 = document.querySelector(".p1");
        if (p1) {
            p1.textContent = format(newDate);
        }
    }, 1000);
</script>
      </font>
    </body>
    <!-- <b><span id="time"></span></b> -->
  </div>
  <ul>
    <li>这是hhhjyz的个人主页</li>
  </ul>
</div> 

<!-- - 基于Material for MkDocs美化
- 简洁美观，功能多元化
- 简单易上手，小白配置
- 𝕙𝕒𝕧𝕖 𝕒 𝕘𝕠𝕠𝕕 𝕥𝕚𝕞𝕖 ! -->



<!-- <strong>推荐文章:material-book:</strong>

  - [利用Mkdocs部署静态网页至GitHub pages](blog/Mkdocs/mkdocs1.md)
  - [Mkdocs部署配置说明(mkdocs.yml)](blog/Mkdocs/mkdocs2.md)
  - [如何给MKdocs添加友链](blog/websitebeauty/linktech.md)
  - [网站添加Mkdocs博客](blog/Mkdocs/mkdocsblog.md)
  - [Blogger](blog/index.md) -->



<div class="grid cards" markdown>

-   :simple-materialformkdocs:{ .lg .middle } __我的课程笔记__

    ---

    - [数据结构基础](docs/notebook/fds)
    


-   :simple-aboutdotme:{ .lg .middle } __技术积累__

    ---
    

</div>
??? success "这是啥"
haha








