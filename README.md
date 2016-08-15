# Firewalld Trusted Updater

MyDNSなどを用いて動的なIPアドレスを管理用端末に使用する場合の
動的IPアドレス変更設定のスクリプト。

今までは、iptablesを用いてきたが、CentOS7からはFirewalldを用いる事が
一般的になってきたので、Firewalldを用いた場合に備えたUpdaterスクリプトを
用意する。

## 1. 今まで iptablesの場合

iptables -N MENTECON
iptables -A MENTECON -s kanri.sennyou.jp -j ACCEPT

などとして、sshやhttpを特定のホストからだけ通過させる。
このkanri.sennyou.jpが変更されることにそなえて、crontabで一定時間毎に

iptables -R MENTECON 1 -s kanri.sennyou.jp -j ACCEPT

などとして、動的IPの変更に追従している。

## 2. Firewalldの場合

管理端末はtrustedゾーンに属することにして、このゾーンのsourceにIPアドレスを設定する。
ただし、ホスト名を直打ち出来ないので、一旦digでIPアドレスに解決してtrustedゾーンに
存在していなければupdateする。

存在していたらスルーする。

これをcrontabで一定時間毎に実行する。


## 必要なプログラム

firewall-cmd
dig

## インストール

    $ sudo install menteconupdate /usr/local/bin
    $ sudo crontab -e
      適当な間隔で、 例えば5分毎に起動させる場合、
        */5 * * * * /usr/local/bin/menteconupdate ホスト名

