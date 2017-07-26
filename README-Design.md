# 设计思路
这里来说下个人对于这个框架和项目的各个方面一个设计思路，不定期更新，想到什么就写什么，欢迎讨论，批评和指正。       
                                      
下文中同样引用<a href="https://github.com/DimitriZhao/sinosteel/blob/master/README-Dev_Guide.md">开发指南</a>中的管理国家标准规范文件的例子来进行一定的叙述，即对于公司收录的国家标准文件需要进行基本的增删改查操作，则需要定义标准文件的相关模型和服务，这个也是framework-example中的例子，可参考framework-example中的代码                  
                        
## 综述
可以想象如下场景：假设你在一家创业公司，公司里面只有你一个架构师。老板为了省钱，招的其它人都是刚毕业的大学生，体力好能干活有热情<del>会暖床</del>，但没法独立构建项目，也不会考虑代码解耦什么的，他们的任务仅仅是使用框架来写业务逻辑。因此，你所开发的框架，就需要做到能够快速地构建新项目以便于接活，能够让开发者尽可能地专注于业务逻辑以便于节省开发成本，尽可能地封装常用的功能以便于快速开发，这样公司才能赚更多的钱，你也能升职加薪，并且那些小同学们在开发的时候才会称赞你这个架构师很imba而不是在心里默念一句mmp。     
               
所以在设计框架的很多机制的时候，我就是想象以上场景来设计的。总而言之就是，最好是开发人员根本不知道某些机制的存在，他们只要按照开发流程去做，最后<del>惊奇地</del>发现某些机制已经被实现了。例如数据权限控制，以国家标准规范文件管理为例，开发者只需要按照开发流程，写好entity，repository，service和controller，然后再把前端做一遍，最后会发现，他们在开发这个模块的时候，业务逻辑里没有写任何关于数据权限控制的逻辑，但最后却实现了数据权限控制，即不同组织的用户对于相应的国家标准规范文件，有着不同的访问，修改，删除权限。这个就是我个人比较希望达到的结果。     
                    
关于框架的基础技术选择，个人选择的是spring boot作为服务端，react/node.js作为客户端，设计为前后端分离的无状态应用框架。原因其实可以说很多，以下为主要原因：                      
               
* 前后端分离的无状态应用是<del>历史的潮流</del>企业应用开发的大趋势。这样的应用非常适合分布式的微服务架构。                                        
* 个人后端的技术栈是java系，并且既然是做前后端分离的无状态应用，肯定后端选择spring boot<del>不要问我为什么不选spring mvc甚至是struts</del>。不过事实上，一开始我本来后端想用spring cloud来做的，配置中心啦网关啦断路器啦什么的我都搞好了，然后突然觉得一个人搞这么多可能吃不消，因为我当时只会jquery，还不会react和es6，前后端一起学并且只有三个半月的时间并且一人单刷前后端并且还有其他项目任务这种情况很可能会要了朕的老命，所以就换成了spring boot。不过这个项目将来我一定要升级为spring cloud。    
                              
* 前端的话有ng2, react和vue可以选。为什么选了react了呢<del>我才不会说我是因为看上了antd的UI了呢</del>？因为ng2出的时间不长，需要进一步<del>暗中</del>观察，vue的生态圈相比于react来说小了些，所以就选了react。一开始这三个我都试过，个人感觉更喜欢react和vue的写法，而且貌似react和vue的技术栈也差不多。但实际上，UI也是个很重要的问题。个人入react坑有很大原因是因为ant design。对于刚入坑的开发<del>玩家</del>，尤其是后端开发来说，自然是喜欢有一整套漂亮的可靠的UI，这样就能轻松地开发出一个美观的app，不然的话都像一开始学web开发的时候写什么`<input id="username" name="username" type="text" />`之类的，做出来的页面就俩输入框，然后一看“卧槽我学半天就写出来这么个丑东西”从而大受打击，再之后调css还要调半天简直心塞。而ant design的文档非常详细，用户基数也大，所以就不怕踩坑，同时因为它是阿里的官方项目因此不像个人项目哪天他老婆心情不好就放着bug坑了。                
                                     
前端的脚手架用的是

https://github.com/OwlAford/easy-react-desktop   
https://github.com/davezuko/react-redux-starter-kit    

这个脚手架的最初的原作者是：             

https://github.com/davezuko             

然后作者说Deprecation Warning，建议使用create-react-app等。。。

好吧应该在2017年4月的时候貌似还没有说这个已经Deprecated。。。           

好在对于这个项目来说，脚手架不是重点。和服务端一样，这个客户端的框架主要提供一个基本CRUD的快速开发，就好比服务端如果换成spring mvc也是照样能用的一样，只要把前端的`src/routes`下面的东西拷到新的脚手架，再配置配置，也是一样能用的              

之后的话，会把前端的项目用<a href="https://github.com/facebookincubator/create-react-app">create-react-app</a>（facebook的官方脚手架）或<a href="https://github.com/dvajs/dva">dvajs</a><del>我喜欢孙dva</del>（antd的官方脚手架）重构下，待老夫先学习一下。。。然后，为什么貌似dvajs好久没更新了<del>我们也爱dva呦</del>。。。
                 
接下来以服务端和客户端进行分述             
                    
## 服务端

  