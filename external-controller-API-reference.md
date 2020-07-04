# Introduction

External Controller enables users to control Clash programmatically with the HTTP RESTful API. The third-party Clash GUIs are heavily based on this feature. Enable this feature by specifying an address in `external-controller`.

> This documentation page is incomplete. Please visit http://clash.gitbook.io/doc/restful-api.

# Table of Contents

* [Getting real-time traffic data](#getting-real-time-traffic-data): `GET /traffic`
* [Getting real-time logs](#getting-real-time-logs): `GET /logs`
* [Getting all proxies](#getting-all-proxies): `GET /proxies`
* [Getting a single proxy](#getting-a-single-proxy): `GET /proxies/:name`
* [Getting the latency of a single proxy](#getting-the-latency-of-a-single-proxy): `GET /proxies/:name/delay`
* [Switching the server of a selector group](#switching-the-server-of-a-selector-group): `PUT /proxies/:name`
* [Getting the general configuration](#getting-the-general-configuration): `GET /configs`
* [Updating the general configuration](#updating-the-general-configuration): `PATCH /configs`
* [Reloading the configuration file](#reloading-the-configuration-file): `PUT /configs`
* [Getting the currently effective rules](#getting-the-currently-effective-rules): `GET /rules`

## Getting real-time traffic data

`GET /traffic`

## Getting real-time logs

`GET /logs`

## Getting all proxies

`GET /proxies`

## Getting a single proxy

`GET /proxies/:name`

## Getting the latency of a single proxy

`GET /proxies/:name/delay`

## Switching the server of a selector group

`PUT /proxies/:name`

## Getting the general configuration

`GET /configs`

## Updating the general configuration

`PATCH /configs`

## Reloading the configuration file

`PUT /configs`

## Getting the currently effective rules

`GET /rules`
