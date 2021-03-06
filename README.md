![ssl error](https://www.cs.cmu.edu/~help/mail_news/images/xp_outlook_7.gif)

# GLoSS (Go SSL)
GLoSS is a very simple HTTPS reverse proxy that allows you to easily develop and test on HTTPS locally

## Why

Your production is on HTTPS, stop developing on HTTP!

Highlights:
- No dependencies. Just one self-contained single executable
- Works on a variety of platforms and architectures: Mac, Linux - Arm7(Raspi2)/Arm6(Raspi)/Amd64/386
- Passes the same headers you'd expect with any other reverse proxy ssl
  - "X-Forwarded-Proto": "https"
  - "X-Forwarded-For": "the.clients.real.ip"
- Doesn't require trusting a CA cert, only valid signing for the hosts you specify (default: *.local.dev)

## Downloads

[Grab the latest release for your platform](https://github.com/mdp/gloss/releases)

## Usage

1. Pick a hostname for your local development
  - `echo "127.0.0.1   local.dev" | sudo tee /etc/hosts > /dev/null`
  - `echo "127.0.0.1   foo.local.dev" | sudo tee /etc/hosts > /dev/null`
1. Create a certificate
  - `gloss setup`
1. Import the certificate to your keychain (Mac specific instructions below)
  - `open ~/.gloss/cert.pem`
  - Find the GLoSS cert and make it "Trusted"
1. Start using GLoSS
  - `gloss --map "*:3000,foo:4000"` Maps foo.local.dev to port 4000, everything else to 3000
1. Visit https://foo.local.dev:4443
  - Will return the content at localhost:4000 via HTTPS

Or just run `gloss -h` for more help


### Setup redirection of port 443 -> 4443

*Mac* (El Capitan and Yosemite)

    echo "rdr pass on lo0 inet proto tcp from any to any port 443 -> 127.0.0.1 port 4443" | sudo pfctl -ef -

*Windows*

    netsh interface portproxy add v4tov4 connectport=4443 listenport=443 connectaddress=127.0.0.1 listenaddress=127.0.0.1

*Linux*

    sudo iptables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT --to-port 4443

## Build from source

`go get github.com/mdp/gloss`

### License: MIT

