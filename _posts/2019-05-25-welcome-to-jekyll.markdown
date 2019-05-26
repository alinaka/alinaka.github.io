---
layout: post
permalink: "/jekyll-custom-domain-on-github-pages"
title:  "Немного о jekyll"
date:   2019-05-25 12:41:16 +0300
categories: jekyll
---
Этот блог работает на [Jekyll](https://jekyllrb.com/docs/), это генератор статичных сайтов.

Я выбрала его по нескольким причинам:
- легковесность
- гибкость
- возможность хостить на [Github Pages](https://pages.github.com/)

[Здесь](https://tom.preston-werner.com/2008/11/17/blogging-like-a-hacker.html) создатель Jekyll пишет о том, как он пришел к идее его создания.

Можно к Github Pages добавить свой домен. Немного [доки на эту тему](https://help.github.com/en/articles/quick-start-setting-up-a-custom-domain).

Вкратце это выглядит так, сначала нужно у своего DNS-провайдера указать нужные записи:
![Configuring A records with your DNS provider](/assets/img/Screenshot-2019-05-25-14:18:16.png)
Что у меня, допустим, на reg.ru выглядит как-то так:
![Настройка ресурсных записей DNS на reg.ru](/assets/img/Screenshot-2019-05-25-14:23:40.png)
Изменения вступят в силу после обновления DNS-серверов (обычно до 24 часов).
Почитать подробнее о [ресурсных записях DNS](https://www.reg.ru/support/dns/Nastroika-zony/chto-takoe-resursnye-zapisi-dns).

Затем в настройках репозитория на Github-е указать свой домен:
![Настройка Github Pages для использования кастомного домена](/assets/img/Screenshot-2019-05-25-14:27:15.png)
что создает в корне репозитория файл CNAME с указанием домена.
