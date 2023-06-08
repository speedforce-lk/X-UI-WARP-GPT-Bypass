# X-UI-WARP-GPT-Bypass
X-UI + WARP for GPT Bypass

Set up xray + VLESS + vision with a cloudflare warp backup

How to add Cloudflare Warp to this setup

But some websites won’t like it if you use an AWS address. Fortunately, they don’t block Cloudflare Warp, so you can set up first connecting to xray and xray will forward whatever domain you want to Cloudflare warp to bypass restrictions. For example, ChatGPT blocks my Amazon IP, but won’t block my Cloudflare warp IP. So we’ll connect the same way, but route via domain names.

First let’s install it and set it up, in this exact order

~~~
curl https://pkg.cloudflareclient.com/pubkey.gpg | sudo gpg --yes --dearmor --output /usr/share/keyrings/cloudflare-warp-archive-keyring.gpg
~~~

~~~
echo 'deb [arch=amd64 signed-by=/usr/share/keyrings/cloudflare-warp-archive-keyring.gpg] https://pkg.cloudflareclient.com/ focal main' | sudo tee /etc/apt/sources.list.d/cloudflare-client.list
~~~

~~~
wget https://github.com/speedforce-demo/X-UI-WARP-GPT-Bypass/raw/main/warp-install && chmod +x warp-install && ./warp-install
~~~

Only when we run it in proxy mode should we turn it on
~~~
warp-cli connect
~~~

then test that it works and you get a different IP:
~~~
curl -4 ip.gs -x socks5://127.0.0.1:10086
~~~
then test that it works for GPT
~~~
curl https://chat.openai.com/cdn-cgi/trace -x socks5://127.0.0.1:10086
~~~

Then go into the settings of x-ui to edit the template in Xray config

add this to outbounds (adding a comma to the previous item)

~~~
{
      "protocol": "socks",
      "settings": {
         "servers": [{
            "address": "127.0.0.1",
            "port": 10086
         }]
      },
      "tag": "warp"
}
~~~


add to routing rules (again, remembering to add a comma)
~~~
{
	"type": "field",
	"outboundTag": "warp",
	"domain": [
		"chat.openai.com"
	]
}
~~~
then save (it will tell you if you made a typo that has a syntax error) and restart the service

Now chatGPT will work even through a proxy!
