# IPv6only-DNS64-NAT64
IPv6 only服务器使用DNS64/NAT64访问IPv4 only网站浅析

现在有一票的云主机商提供非常廉价的IPv6 only云主机像Vultr的2.5美金/月，那这个IPv6 only云主机能够访问IPv4 only的网站吗？具体像www.baidu.com 是典型的DNS只有A记录的网站，IPv6 only云主机铁定访问不了，IPv6 only云主机只能访问IPv6.baidu.com  

但是像github.com就无能为力了，因为github.com只有DNS的A记录，网站压根就没有DNS的AAAA记录，如果IPv6 only云主机没有特殊配置连github.com都上不了，在这个年代上不了github.com的主机还不如垃圾  

在展开话题前还是讲一下，要便宜可以，没有必要折腾得那么复杂，因为市面上有有很多NAT VPS云服务商，像hosting.gullo.me和mrvm.net等等，是几美金一年（重复一次，壹年），一般提供一个共享IPv4地址，IPv4的端口是20+1 ssh，带宽无限制，然后IPv6公网地址这块每台VPS提供/80的子网，即2的48次方数量的公网IPv6地址分给你，对比现在整个地球的IPv4地址数量才区区2的32次方，所以分给你的公网IPv6地址都可以让你再组建一个全球互联网了，而且IPv6的端口是没有限制的，全开放，关键是这些机器具备双栈，即自由访问IPv4和IPv6没有障碍，使用起来与一般IPv4 only的VPS没有太大区别，其中区别明显是一般NAT VPS都是OpenVZ方式，那意味着不能开启BBR加速。但是你会上CDN的话，这个就不是问题了，因为CDN的骨干网传输你也影响不了不是？这种情况下BBR是废的   

扯远了，回到IPv6 only服务器使用DNS64/NAT64访问IPv4 only网站的话题，标准RFC的书包就不丢了，简单来讲IPv6有2的128次方的地址数量，对比于IPv4的2的32次方地址数量，IPv6随便给一个/96的子网就能把现在的IPv4互联网地址给全1:1映射了，所以当时制定IPv6标准的时候就很大方的预留了一个/16保留段地址头（有兴趣的机友可以查这个保留段的地址头）的子网段给IPv4折腾映射，可以映射现在整个IPv4互联网2的80次方那么多次   
   
有了这个子网段映射，相应的就有DNS64这个从IPv6寻找IPv4域名，然后映射成IPv6虚地址的DNS解析标准，也相应的有DNS64域名服务器，如谷歌的DNS64服务器介绍连接“https://developers.google.com/speed/public-dns/docs/dns64”， Google Public DNS64  |  Google Developers  ，当然这些公共的DNS64服务提供商还有很多  
   
有了这个DNS64服务器，那这个IPv6 only云主机能够访问IPv4 only的网站吗？机友们高兴太早了，DNS64域名服务器只是给你映射好这个地址，至于谁去把左面IPv6虚地址访问右面IPv4实地址的会话过程Hold住，像NAT一样为机友们工作呢？这个就像上面谷歌连接里面轻飘飘的说道，不要找我，我是吃瓜群众，你找NAT64服务器去     
   
唔......NAT这个名词好耳熟呀？对了，的确是有制定标准NAT64服务的，不过这是个苦差事，实际上NAT64服务器是把所有左面的IPv6虚地址访问右面IPv4实地址的会话过程Hold住，为IPv6 only服务器访问IPv4服务器充当代理服务器，所有这种流量都经过它，一个包不落下，当然IPv6到IPv6的访问又和它一毛钱关系没有    

知道了上面这些关系，那个IPv6 only云主机有两种方法解决访问IPv4网站的问题    
     
第一种，自己买一台既有IPv4地址又有IPv6地址的双栈VPS服务器，把DNS服务器地址设置成DNS64服务器地址，如谷歌的“2001:4860:4860::64”，再搭一个NAT64服务，然后IPv6 only云主机的DNS地址配置为这台双栈VPS服务器的IPv6公网地址，这样IPv6 only云主机就可以通过自己搭的NAT64服务器访问IPv4的网站了。写到这里我自己都※＃＠＆＆※＃，脑子有毛病，已经可以买双栈VPS服务器了，还去折腾个什么IPv6 only狗屁云主机，毕竟我们是要平仓，而不是摊薄成本      
      
第二种，赶紧平仓，找个DNS64/NAT64二合一义工服务器，把义工服务器的地址设置成IPv6 only云主机的DNS地址，如“2a09:11c0:100:0:0:0:0:53”，这样就可以了，不过这些个义工也知道那些IPv6 only云奸商要甩包袱，所以这些义工也封锁了好些IPv6 only云奸商的所有地址段，所以这些NAT64地址要具体设进去IPv6 only云主机的DNS地址，然后用下面命令访问一下IPv4 only的网站，有内容返回表示这个NAT64地址对现在这台IPv6 only云主机的地址段是work的   
   
curl github.com  
   
curl www.baidu.com  
    
无论是第一种方案还是第二种方案，都要注意一个问题是，如果要利用这台IPv6 only云主机转发客户端到互联网的访问，那么就要小心这个客户端用什么协议查询传统的IPv4 DNS了，因为NAT64非常在意实际客户端使用什么协议查询传统的IPv4 DNS，如果协议不匹配，即使客户端连通了这台IPv6 only云主机，也会表现出不能访问任何IPv4网站的症状   
   
DNS64/NAT64二合一义工服务器列表：   
https://nat64.xyz/    


