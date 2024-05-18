# IPV6-VMSHELL
VmShell美国仅IPV6地址服务器完整教程(WGCF安装和自动启动)
<p>前段时间撸了VmShell提供的美国仅有IPV6地址的免费VPS,由于厂家当时没有设置DNS64地址,我们都是手动修改设置了DNS64解析（允许IPV6地址的服务器去访问IPV4的网站）,昨日VMSHELL宣告修复了DNS64,所以我们今天重新出一份仅有IPV6地址的服务器如何使用和搭建。最大的问题就是不是所有的网站都有IPV6地址，所有呢，就是纯IPV6 的VPS不能访问所有网站。目前已知的解决方法是，使用DNS64解析 或者就是Cloudflare的WGCF。多次测试后发现还是WGCF功能强大。简单记录以下 ：VmShell使用的debian11系统安装WGCF。同时VmShell和ToToTel最新产品活动的折扣信息包含IP地址信息查询请见:<a href="https://vmshell.com/ip.php" target="_blank" rel="noopener">https://vmshell.com/ip.php</a> </p>
<p><img class="alignnone size-full wp-image-36426" title="e3ec0cd6c409a9e70fb997202bc7e8fd" src="https://linuxword.com/wp-content/uploads/2024/05/e3ec0cd6c409a9e70fb997202bc7e8fd.png" alt="e3ec0cd6c409a9e70fb997202bc7e8fd" width="1050" height="1019" /></p>
<p><strong>IPV6服务器启动所需要的关键词语</strong>：<br />
1,<strong>如何连接IPV6地址的服务器，安装和启动WGCF</strong>；<br />
2,<strong>如何更新服务器组件/启用BBR+FQ协议</strong>；<br />
3,<strong>如何发挥小内存的服务器最大优势,定时清理内存缓存,提升内存使用率</strong>；<br />
4,<strong>如何申请免费的泛域名SSL文件，上传到服务器备用</strong>；<br />
5,<strong>搭建X-UI并设置VMESS+TLS+WS实现网络功能</strong>；</p>
<p><a href="https://linuxword.com/wp-content/uploads/2024/05/vmshell-ipv6pic.jpg"><img class="alignnone size-full wp-image-36401" src="https://linuxword.com/wp-content/uploads/2024/05/vmshell-ipv6pic.jpg" alt="" width="1051" height="792" /></a></p>
<p>下面我们就正式开始了,首先进入控制面板获取服务器的密码：<br />
第一步：连接服务器SSH</p>
<p><img class="alignnone size-full wp-image-36406" title="51e394266af94e13faf3c615cc45e609" src="https://linuxword.com/wp-content/uploads/2024/05/51e394266af94e13faf3c615cc45e609.png" alt="51e394266af94e13faf3c615cc45e609" width="1301" height="866" /></p>
<p><strong>第二步</strong>：更新服务器组件/启用BBR+FQ协议，并安装和启动WGCF(大体步骤分为2步。1是生成wgcf 配置文件。2是把配置文件放进客户端里去),整合VmShell提供的Debian11系统磁盘 (Debian11和Ubuntu20需要使用) ，设置后可以输入 df-h查看硬盘情况,以及更新服务器组件代码：</p>
<p><img class="alignnone size-full wp-image-36410" title="fc31399b1578ee7dd2d3d7427bb92e86" src="https://linuxword.com/wp-content/uploads/2024/05/fc31399b1578ee7dd2d3d7427bb92e86.png" alt="fc31399b1578ee7dd2d3d7427bb92e86" width="1325" height="847" /><br />
resize2fs -f /dev/vda1 &amp;&amp; apt -y update &amp;&amp; apt -y install nano socat dnsutils libaio1 libaio-dev build-essential manpages-dev libncurses5 zip gnupg libaio1 wget curl screen unzip vim curl xz-utils openssl gawk file rpm cron &amp;&amp; apt -y upgrade &amp;&amp; df -h &amp;&amp; screen -S setupscreen</p>
<p>启用BBR+FQ协议(后reboot重启)脚本代码:</p>
<p>wget -O tcp.sh "https://git.io/coolspeeda" &amp;&amp; chmod +x tcp.sh &amp;&amp; ./tcp.sh</p>
<p><a href="https://linuxword.com/wp-content/uploads/2022/11/f9c60413f27d6b02ebf66f5dddf73058.png"><img class="alignnone size-full wp-image-17319" src="https://linuxword.com/wp-content/uploads/2022/11/f9c60413f27d6b02ebf66f5dddf73058.png" sizes="(max-width: 912px) 100vw, 912px" srcset="https://linuxword.com/wp-content/uploads/2022/11/f9c60413f27d6b02ebf66f5dddf73058.png 912w, https://linuxword.com/wp-content/uploads/2022/11/f9c60413f27d6b02ebf66f5dddf73058-768x517.png 768w" alt="" width="912" height="614" /></a></p>
<p>安装和启动WGCF(大体步骤分为2步。1是生成WGCF配置文件。2是把配置文件放进客户端里去),我们弄个文件夹方便管理相关文件<br />
mkdir wgcf &amp;&amp; cd wgcf<br />
#下载对应程序以及添加执行权限脚本:<br />
wget -O wgcf https://github.com/ViRb3/wgcf/releases/download/v2.2.13/wgcf_2.2.13_linux_amd64 &amp;&amp; chmod +x wgcf</p>
<p><img class="alignnone size-full wp-image-36415" title="c5babb5d0723a58a6a97bc3e1db89620" src="https://linuxword.com/wp-content/uploads/2024/05/c5babb5d0723a58a6a97bc3e1db89620.png" alt="c5babb5d0723a58a6a97bc3e1db89620" width="1325" height="847" />#注册WARP账户<br />
./wgcf register<br />
#生成WireGuard配置文件<br />
./wgcf generate<br />
这里生成的配置文件有两个，wgcf-account.toml和wgcf-profile.conf 。其中一个是账户信息，一个是配置文件，需要修改下配置文件wgcf-profile.conf 。生成的两个文件记得备份好，尤其是 wgcf-profile.conf，万一未来工具失效、重装系统后可能还用得着。</p>
<p><img class="alignnone size-full wp-image-36416" title="e332142902e79cd30d7a83c13f49f28a" src="https://linuxword.com/wp-content/uploads/2024/05/e332142902e79cd30d7a83c13f49f28a.png" alt="e332142902e79cd30d7a83c13f49f28a" width="1325" height="847" /><br />
配置文件大概长下面的样子 。如果是需要ipv4 ，就用下面的配置。需要注释掉第10行，修改第11行，照下面的修改就可以了 （命令：nano wgcf-profile.conf）</p>
<p><strong><img class="alignnone size-full wp-image-36417" title="1188b8c11609aed3e8bf40d41f170e8b" src="https://linuxword.com/wp-content/uploads/2024/05/1188b8c11609aed3e8bf40d41f170e8b.png" alt="1188b8c11609aed3e8bf40d41f170e8b" width="1325" height="847" /></strong></p>
<p>将配置文件中的节点域名 engage.cloudflareclient.com 解析成 IP。不过一般都是以下两个结果：<br />
162.159.192.1<br />
2606:4700:d0::a29f:c001</p>
<p><img class="alignnone size-full wp-image-36418" title="d8a55f814fe6f7ffb2cacb703b83ff40" src="https://linuxword.com/wp-content/uploads/2024/05/d8a55f814fe6f7ffb2cacb703b83ff40.png" alt="d8a55f814fe6f7ffb2cacb703b83ff40" width="1240" height="646" /></p>
<p>完整修改后如图所示：</p>
<p><img class="alignnone size-full wp-image-36419" title="99498f6b4d2dac0df1c2588fd7ac49f8" src="https://linuxword.com/wp-content/uploads/2024/05/99498f6b4d2dac0df1c2588fd7ac49f8.png" alt="99498f6b4d2dac0df1c2588fd7ac49f8" width="616" height="255" /></p>
<p>如果需要ipv6的就用下面的配置。注释第9行，修改第11行。<br />
[Interface]<br />
PrivateKey = AHINxxVlZxRjKvtqTrvYR9oxOJmorW5JAmct5q/wRkI=<br />
Address = 172.16.0.2/32<br />
Address = fd01:5ca1:ab1e:8e8a:7a8d:919e:bb2:93aa/128<br />
DNS = 1.1.1.1<br />
MTU = 1280<br />
[Peer]<br />
PublicKey = bmXOC+F1FxEMF9dyiK2H5/1SUtzH0JuVo51h2wPfgyo=<br />
#AllowedIPs = 0.0.0.0/0<br />
AllowedIPs = ::/0<br />
Endpoint = 162.159.192.1:2408</p>
<p><img class="alignnone size-full wp-image-36420" title="dfc317fd9502b0cd03e9d85e171650a0" src="https://linuxword.com/wp-content/uploads/2024/05/dfc317fd9502b0cd03e9d85e171650a0.png" alt="dfc317fd9502b0cd03e9d85e171650a0" width="1325" height="847" /></p>
<p>下面是安装客户端。<br />
#Debian添加unstable源<br />
echo "deb http://deb.debian.org/debian/ unstable main" &gt; /etc/apt/sources.list.d/unstable-wireguard.list<br />
printf 'Package: *\nPin: release a=unstable\nPin-Priority: 150\n' &gt; /etc/apt/preferences.d/limit-unstable<br />
#更新源并安装<br />
apt -y update &amp;&amp; apt -y install wireguard-dkms wireguard-tools<br />
#Ubuntu添加库<br />
add-apt-repository ppa:wireguard/wireguard<br />
#更新源并安装<br />
apt -y update &amp;&amp; apt-get install wireguard<br />
#安装openresolv 工具<br />
apt -y install openresolv<br />
然后把wgcf-profile.conf 复制到 /etc/wireguard. 修改文件名为wgcf.conf<br />
mv ./wgcf-profile.conf /etc/wireguard/wgcf.conf</p>
<p><img class="alignnone size-full wp-image-36421" title="810ee5817422aa6decc5afd4d0e50b77" src="https://linuxword.com/wp-content/uploads/2024/05/810ee5817422aa6decc5afd4d0e50b77.png" alt="810ee5817422aa6decc5afd4d0e50b77" width="1325" height="847" /><br />
#开启隧道<br />
wg-quick up wgcf<br />
#关闭隧道<br />
wg-quick down wgcf</p>
<p>执行执行ip a命令，此时能看到名为wgcf的网络接口，类似于下面这张图</p>
<p><strong><img class="alignnone size-full wp-image-36422" title="3feff64435f2bfe1a09cce233a2ce762" src="https://linuxword.com/wp-content/uploads/2024/05/3feff64435f2bfe1a09cce233a2ce762.png" alt="3feff64435f2bfe1a09cce233a2ce762" width="1325" height="847" /></strong></p>
<p>执行以下命令检查是否连通。同时也能看到正在使用的是 Cloud­flare 的网络。<br />
# IPv4 Only VPS <br />
curl -6 ip.p3terx.com <br />
# IPv6 Only VPS <br />
curl -4 ip.p3terx.com</p>
<p><strong><img class="alignnone size-full wp-image-36423" title="156bc09c512a51a6ab2c2cfa32ed9c5a" src="https://linuxword.com/wp-content/uploads/2024/05/156bc09c512a51a6ab2c2cfa32ed9c5a.png" alt="156bc09c512a51a6ab2c2cfa32ed9c5a" width="469" height="169" /></strong></p>
<p>测试完成后关闭相关接口，因为这样配置只是临时性的。现在我们让他永久启动:</p>
<p>执行关闭脚本：wg-quick down wgcf</p>
<p><span style="color: #ff0000;"><strong>正式启用 Wire­Guard 网络接口 ，开机自动启动获取IPV4</strong></span><br />
# 启用守护进程 <br />
systemctl start wg-quick@wgcf <br />
# 设置开机启动 <br />
systemctl enable wg-quick@wgcf</p>
<p><strong><img class="alignnone size-full wp-image-36425" title="04f0b2eff03ef12564a9b392904a114c" src="https://linuxword.com/wp-content/uploads/2024/05/04f0b2eff03ef12564a9b392904a114c.png" alt="04f0b2eff03ef12564a9b392904a114c" width="1000" height="220" /></strong></p>
<p><strong>第三步:</strong>如何发挥小内存的服务器最大优势,定时清理内存缓存,提升内存使用率；(下载附件<a href="https://linuxword.com/wp-content/uploads/2024/05/script.zip">script</a>),把他保存到目录：/opt/script/   并且修改权限,见图 </p>
<p><img class="alignnone size-full wp-image-36413" title="8277c88cac0d14eb1986ae240668a59f" src="https://linuxword.com/wp-content/uploads/2024/05/8277c88cac0d14eb1986ae240668a59f.png" alt="8277c88cac0d14eb1986ae240668a59f" width="446" height="549" /></p>
<p>修改权限：chmod -R 777 /opt/script/cron<br />
然后将两句执行的话放入到计划启动中：输入命令：crontab -e 执行后输入下面内容<br />
*/9 * * * * sh /opt/script/cron/cleanCache.sh<br />
*/8 * * * * sh /opt/script/cron/cleanlog.sh<br />
保存退出 后reboot 服务器</p>
<p><img class="alignnone size-full wp-image-36414" title="5c88106c16b623036df810d29e5551a1" src="https://linuxword.com/wp-content/uploads/2024/05/5c88106c16b623036df810d29e5551a1.png" alt="5c88106c16b623036df810d29e5551a1" width="1325" height="847" /></p>
<p>第四步：如何申请免费的泛域名SSL文件，并且上传到服务器备用</p>
<p>用另外一台IPV4的服务器申请，教材请见：<a href="https://linuxword.com/?p=6214" target="_blank" rel="noopener">Debian10 (64Bit)申请泛域名SSL(Zero)-已验证</a></p>
<p>1、安装必备工具<br />
apt -y update &amp;&amp; apt -y install nano socat dnsutils libaio1 libaio-dev build-essential manpages-dev libncurses5 zip gnupg libaio1 wget curl screen unzip vim curl xz-utils openssl gawk file rpm &amp;&amp; screen -S setupscreen</p>
<p><img class="alignnone size-full wp-image-6215" title="e3eb5ca505b989d76abfd92672362455" src="https://linuxword.com/wp-content/uploads/2022/04/e3eb5ca505b989d76abfd92672362455.png" sizes="(max-width: 1678px) 100vw, 1678px" srcset="https://linuxword.com/wp-content/uploads/2022/04/e3eb5ca505b989d76abfd92672362455.png 1678w, https://linuxword.com/wp-content/uploads/2022/04/e3eb5ca505b989d76abfd92672362455-1024x520.png 1024w, https://linuxword.com/wp-content/uploads/2022/04/e3eb5ca505b989d76abfd92672362455-768x390.png 768w, https://linuxword.com/wp-content/uploads/2022/04/e3eb5ca505b989d76abfd92672362455-1536x780.png 1536w" alt="e3eb5ca505b989d76abfd92672362455" width="1678" height="852" /></p>
<p>2、安装和启用acme脚本<br />
curl https://get.acme.sh | sh -s email=my@domain.com<br />
source ~/.bashrc</p>
<p><img class="alignnone size-full wp-image-6216" title="43a83c59ffe85d7cab93ae6a05d8dbca" src="https://linuxword.com/wp-content/uploads/2022/04/43a83c59ffe85d7cab93ae6a05d8dbca.png" sizes="(max-width: 1678px) 100vw, 1678px" srcset="https://linuxword.com/wp-content/uploads/2022/04/43a83c59ffe85d7cab93ae6a05d8dbca.png 1678w, https://linuxword.com/wp-content/uploads/2022/04/43a83c59ffe85d7cab93ae6a05d8dbca-1024x520.png 1024w, https://linuxword.com/wp-content/uploads/2022/04/43a83c59ffe85d7cab93ae6a05d8dbca-768x390.png 768w, https://linuxword.com/wp-content/uploads/2022/04/43a83c59ffe85d7cab93ae6a05d8dbca-1536x780.png 1536w" alt="43a83c59ffe85d7cab93ae6a05d8dbca" width="1678" height="852" /></p>
<p>3,查询版本: ~/.acme.sh/acme.sh -v<br />
<img class="alignnone size-full wp-image-6217" title="46abc43ae941698c65f4bf7ce302fba2" src="https://linuxword.com/wp-content/uploads/2022/04/46abc43ae941698c65f4bf7ce302fba2.png" sizes="(max-width: 1603px) 100vw, 1603px" srcset="https://linuxword.com/wp-content/uploads/2022/04/46abc43ae941698c65f4bf7ce302fba2.png 1603w, https://linuxword.com/wp-content/uploads/2022/04/46abc43ae941698c65f4bf7ce302fba2-1024x544.png 1024w, https://linuxword.com/wp-content/uploads/2022/04/46abc43ae941698c65f4bf7ce302fba2-768x408.png 768w, https://linuxword.com/wp-content/uploads/2022/04/46abc43ae941698c65f4bf7ce302fba2-1536x816.png 1536w" alt="46abc43ae941698c65f4bf7ce302fba2" width="1603" height="852" /></p>
<p>4、申请证书，需要防火墙打开80端口，并且不能被占用，邮箱可以随意，domain.com为你自己申请的域名<br />
~/.acme.sh/acme.sh --register-account -m admin@xxxx.com</p>
<p><img class="alignnone size-full wp-image-6218" title="4f0cd2927a769768beaf52481424e8c2" src="https://linuxword.com/wp-content/uploads/2022/04/4f0cd2927a769768beaf52481424e8c2.png" sizes="(max-width: 1678px) 100vw, 1678px" srcset="https://linuxword.com/wp-content/uploads/2022/04/4f0cd2927a769768beaf52481424e8c2.png 1678w, https://linuxword.com/wp-content/uploads/2022/04/4f0cd2927a769768beaf52481424e8c2-1024x520.png 1024w, https://linuxword.com/wp-content/uploads/2022/04/4f0cd2927a769768beaf52481424e8c2-768x390.png 768w, https://linuxword.com/wp-content/uploads/2022/04/4f0cd2927a769768beaf52481424e8c2-1536x780.png 1536w" alt="4f0cd2927a769768beaf52481424e8c2" width="1678" height="852" /></p>
<p>5,开始通过DNS模式申请: ~/.acme.sh/acme.sh --issue -d linuxword.com -d *.linuxword.com --dns --yes-I-know-dns-manual-mode-enough-go-ahead-please<br />
或者:~/.acme.sh/acme.sh --issue -d linuxword.com -d *.linuxword.com --dns \--yes-I-know-dns-manual-mode-enough-go-ahead-please</p>
<p><img class="alignnone size-full wp-image-6219" title="e6594e405b838b41dc35173a62d624dd" src="https://linuxword.com/wp-content/uploads/2022/04/e6594e405b838b41dc35173a62d624dd.png" sizes="(max-width: 1678px) 100vw, 1678px" srcset="https://linuxword.com/wp-content/uploads/2022/04/e6594e405b838b41dc35173a62d624dd.png 1678w, https://linuxword.com/wp-content/uploads/2022/04/e6594e405b838b41dc35173a62d624dd-1024x520.png 1024w, https://linuxword.com/wp-content/uploads/2022/04/e6594e405b838b41dc35173a62d624dd-768x390.png 768w, https://linuxword.com/wp-content/uploads/2022/04/e6594e405b838b41dc35173a62d624dd-1536x780.png 1536w" alt="e6594e405b838b41dc35173a62d624dd" width="1678" height="852" /></p>
<p>6, 将上一步生成的TXT记录手动添加到指定域名采用如下 Renew命令执行生成CRT证书文件<br />
~/.acme.sh/acme.sh --renew -d linuxword.com -d *.linuxword.com --yes-I-know-dns-manual-mode-enough-go-ahead-please<br />
或者:<br />
~/.acme.sh/acme.sh --renew -d linuxword.com -d *.linuxword.com \--yes-I-know-dns-manual-mode-enough-go-ahead-please</p>
<p><img class="alignnone size-full wp-image-6220" title="bef01f4542e156e3836260bdbdaa20fc" src="https://linuxword.com/wp-content/uploads/2022/04/bef01f4542e156e3836260bdbdaa20fc.png" sizes="(max-width: 1678px) 100vw, 1678px" srcset="https://linuxword.com/wp-content/uploads/2022/04/bef01f4542e156e3836260bdbdaa20fc.png 1678w, https://linuxword.com/wp-content/uploads/2022/04/bef01f4542e156e3836260bdbdaa20fc-1024x520.png 1024w, https://linuxword.com/wp-content/uploads/2022/04/bef01f4542e156e3836260bdbdaa20fc-768x390.png 768w, https://linuxword.com/wp-content/uploads/2022/04/bef01f4542e156e3836260bdbdaa20fc-1536x780.png 1536w" alt="bef01f4542e156e3836260bdbdaa20fc" width="1678" height="852" /></p>
<p><img class="alignnone size-full wp-image-6223" title="9a29492925d97ba2e12a2c5649202b5a" src="https://linuxword.com/wp-content/uploads/2022/04/9a29492925d97ba2e12a2c5649202b5a.png" sizes="(max-width: 1604px) 100vw, 1604px" srcset="https://linuxword.com/wp-content/uploads/2022/04/9a29492925d97ba2e12a2c5649202b5a.png 1604w, https://linuxword.com/wp-content/uploads/2022/04/9a29492925d97ba2e12a2c5649202b5a-1024x815.png 1024w, https://linuxword.com/wp-content/uploads/2022/04/9a29492925d97ba2e12a2c5649202b5a-768x611.png 768w, https://linuxword.com/wp-content/uploads/2022/04/9a29492925d97ba2e12a2c5649202b5a-1536x1223.png 1536w" alt="9a29492925d97ba2e12a2c5649202b5a" width="1604" height="1277" />如果失败了，可以多试两次。acme.sh会自动为证书续期。申请完证书最后会打印出证书存放的路径，这里只需要2个文件的绝对路径，一般路径是<br />
/root/.acme.sh/linuxword.com/domain.com.cer<br />
/root/.acme.sh/domain.com/domain.com.key<br />
下载证书迁移目录: cp -r /root/.acme.sh/linuxword.com/ /home/All-SSL-File<br />
证书拷贝到Nginx目录<br />
.acme.sh/acme.sh --installcert -d 你的域名 --fullchainpath /etc/ssl/private/你的域名.crt --keypath /etc/ssl/private/你的域名.key --ecc<br />
~/.acme.sh/acme.sh --installcert -d *.domain.com \<br />
--key-file /usr/local/nginx/conf/vhost/acme/domain.com.key.pem \<br />
--fullchain-file /usr/local/nginx/conf/vhost/acme/domain.com.cert.pem \<br />
--reloadcmd "service nginx reload"</p>
<p><img class="alignnone size-full wp-image-6227" title="267926b0724a2e84910e68f3b0a3dbf9" src="https://linuxword.com/wp-content/uploads/2022/04/267926b0724a2e84910e68f3b0a3dbf9.png" alt="267926b0724a2e84910e68f3b0a3dbf9" width="737" height="920" /></p>
<p>第五步：搭建X-UI并设置VMESS+TLS+WS实现网络功能；</p>
<p><a href="https://linuxword.com/wp-content/uploads/2022/11/76ad824b7f7fe5a2a5a46a16d0a8da9d.png"><img class="alignnone size-full wp-image-17314" src="https://linuxword.com/wp-content/uploads/2022/11/76ad824b7f7fe5a2a5a46a16d0a8da9d.png" sizes="(max-width: 1378px) 100vw, 1378px" srcset="https://linuxword.com/wp-content/uploads/2022/11/76ad824b7f7fe5a2a5a46a16d0a8da9d.png 1378w, https://linuxword.com/wp-content/uploads/2022/11/76ad824b7f7fe5a2a5a46a16d0a8da9d-1024x452.png 1024w, https://linuxword.com/wp-content/uploads/2022/11/76ad824b7f7fe5a2a5a46a16d0a8da9d-768x339.png 768w" alt="" width="1378" height="608" /></a></p>
<p>Github裏的大佬們用Go語言，重新開發了可視化管理面板X-ui，可完全替代V2-ui，可自建SS/V2ray/Xray/Trojan等熱門的協議，並可以實時查看VPS服務器性能狀態和流量的使用狀態。它兼容性更強、更易於維護、性能更好、內存佔用也低</p>
<p>安装X-ui面板,通过一键安装脚本;<br />
bash &lt;(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)</p>
<p><img class="alignnone size-full wp-image-17328" title="07554fe5cbeae845f0ba731aa5fae217" src="https://linuxword.com/wp-content/uploads/2022/11/07554fe5cbeae845f0ba731aa5fae217.png" sizes="(max-width: 1212px) 100vw, 1212px" srcset="https://linuxword.com/wp-content/uploads/2022/11/07554fe5cbeae845f0ba731aa5fae217.png 1212w, https://linuxword.com/wp-content/uploads/2022/11/07554fe5cbeae845f0ba731aa5fae217-1024x633.png 1024w, https://linuxword.com/wp-content/uploads/2022/11/07554fe5cbeae845f0ba731aa5fae217-768x475.png 768w" alt="07554fe5cbeae845f0ba731aa5fae217" width="1212" height="749" /><br />
登录后:启动并修改为443端口[选择保存配置,重启面板] ,目前网址:<a href="http://xray.way-run.com:54321/"> http://xray.way-run.com:54321</a></p>
<p><img class="alignnone size-full wp-image-17330" title="16b524c7a898d3997d28cba7a4baa3a1" src="https://linuxword.com/wp-content/uploads/2022/11/16b524c7a898d3997d28cba7a4baa3a1.png" sizes="(max-width: 1180px) 100vw, 1180px" srcset="https://linuxword.com/wp-content/uploads/2022/11/16b524c7a898d3997d28cba7a4baa3a1.png 1180w, https://linuxword.com/wp-content/uploads/2022/11/16b524c7a898d3997d28cba7a4baa3a1-1024x533.png 1024w, https://linuxword.com/wp-content/uploads/2022/11/16b524c7a898d3997d28cba7a4baa3a1-768x400.png 768w" alt="16b524c7a898d3997d28cba7a4baa3a1" width="1180" height="614" /></p>
<p><img class="alignnone size-full wp-image-17331" title="54095a497ef8722948ddeaa8a3380de4" src="https://linuxword.com/wp-content/uploads/2022/11/54095a497ef8722948ddeaa8a3380de4.png" sizes="(max-width: 2560px) 100vw, 2560px" srcset="https://linuxword.com/wp-content/uploads/2022/11/54095a497ef8722948ddeaa8a3380de4.png 2560w, https://linuxword.com/wp-content/uploads/2022/11/54095a497ef8722948ddeaa8a3380de4-1024x574.png 1024w, https://linuxword.com/wp-content/uploads/2022/11/54095a497ef8722948ddeaa8a3380de4-768x431.png 768w, https://linuxword.com/wp-content/uploads/2022/11/54095a497ef8722948ddeaa8a3380de4-1536x861.png 1536w, https://linuxword.com/wp-content/uploads/2022/11/54095a497ef8722948ddeaa8a3380de4-2048x1148.png 2048w" alt="54095a497ef8722948ddeaa8a3380de4" width="2560" height="1435" /></p>
<p><strong>保存配置后重启面板就可以访问</strong>： <a href="https://xray.way-run.com/">https://xray.way-run.com</a>  直接访问了<br />
<strong>节点生成</strong>：(Vmess+TSL+WS协议) 现在生成第一个链接设置(很重要 ): 为了防止IP如果被封如何访问的问题，请使用CloudFlare的小云朵开启后能访问特定端口的方式做节点和链接的端口生成，现在将CloudFlare的特定端口列出来给大家分享如下：</p>
<p><img class="alignnone size-full wp-image-17332" title="6054f7217f09778ac60aa9280090433b" src="https://linuxword.com/wp-content/uploads/2022/11/6054f7217f09778ac60aa9280090433b.png" sizes="(max-width: 1586px) 100vw, 1586px" srcset="https://linuxword.com/wp-content/uploads/2022/11/6054f7217f09778ac60aa9280090433b.png 1586w, https://linuxword.com/wp-content/uploads/2022/11/6054f7217f09778ac60aa9280090433b-1024x876.png 1024w, https://linuxword.com/wp-content/uploads/2022/11/6054f7217f09778ac60aa9280090433b-768x657.png 768w, https://linuxword.com/wp-content/uploads/2022/11/6054f7217f09778ac60aa9280090433b-1536x1314.png 1536w" alt="6054f7217f09778ac60aa9280090433b" width="1586" height="1357" /><br />
使用方式：如果您IP被封了请登录CloudFlare开启这个子域名的小云朵，等什么时候您IP被解封了之后，您再关闭小云朵取消代理即可，一次设置，终身受益,如果您采用CloudFlare的免费小云朵方式拯救您被中国大陆墙掉的IP，CloudFlare默认可以小云朵访问的端口如下：<br />
HTTP使用的端口：<strong>80 、8080、8880、2052、2082、2086、2095</strong><br />
HTTPs使用的端口：<strong>443、2053、2083、2087、2096、8443</strong><br />
顺便说一句：Finalshell很垃圾，请用Xshell专业软件<br />
WinSCP绿色软件下载点击此处:<a class="autoLinked" href="https://linuxword.com/wp-content/uploads/2021/11/WinSCP.zip" target="_blank" rel="noreferrer noopener">https://linuxword.com/wp-content/uploads/2021/11/WinSCP.zip</a><br />
Xshell绿色破解版下载点击此处:<a class="autoLinked" href="https://linuxword.com/wp-content/uploads/2021/11/Xshell.zip" target="_blank" rel="noreferrer noopener">https://linuxword.com/wp-content/uploads/2021/11/Xshell.zip</a></p>
<p><img class="alignnone size-full wp-image-17334" title="cbd59b677bf16392ba7e7d8de384c89a" src="https://linuxword.com/wp-content/uploads/2022/11/cbd59b677bf16392ba7e7d8de384c89a.png" sizes="(max-width: 2560px) 100vw, 2560px" srcset="https://linuxword.com/wp-content/uploads/2022/11/cbd59b677bf16392ba7e7d8de384c89a.png 2560w, https://linuxword.com/wp-content/uploads/2022/11/cbd59b677bf16392ba7e7d8de384c89a-1024x574.png 1024w, https://linuxword.com/wp-content/uploads/2022/11/cbd59b677bf16392ba7e7d8de384c89a-768x431.png 768w, https://linuxword.com/wp-content/uploads/2022/11/cbd59b677bf16392ba7e7d8de384c89a-1536x861.png 1536w, https://linuxword.com/wp-content/uploads/2022/11/cbd59b677bf16392ba7e7d8de384c89a-2048x1148.png 2048w" alt="cbd59b677bf16392ba7e7d8de384c89a" width="2560" height="1435" /></p>
<p>如果你IP被封了的情况下？如何启用CloudFlare解决你IP被墙的问题？等IP放出来了再恢复即可，简单说：解决IP被墙，因为端口号是跟cloudflare一致的，只需要修改启动”<strong>小云朵</strong>“即可！</p>
<p><img class="alignnone size-full wp-image-17343" title="c424090b2b7818db5504d71af60f925a" src="https://linuxword.com/wp-content/uploads/2022/11/c424090b2b7818db5504d71af60f925a.png" sizes="(max-width: 2095px) 100vw, 2095px" srcset="https://linuxword.com/wp-content/uploads/2022/11/c424090b2b7818db5504d71af60f925a.png 2095w, https://linuxword.com/wp-content/uploads/2022/11/c424090b2b7818db5504d71af60f925a-1024x404.png 1024w, https://linuxword.com/wp-content/uploads/2022/11/c424090b2b7818db5504d71af60f925a-768x303.png 768w, https://linuxword.com/wp-content/uploads/2022/11/c424090b2b7818db5504d71af60f925a-1536x606.png 1536w, https://linuxword.com/wp-content/uploads/2022/11/c424090b2b7818db5504d71af60f925a-2048x808.png 2048w" alt="c424090b2b7818db5504d71af60f925a" width="2095" height="827" /></p>
