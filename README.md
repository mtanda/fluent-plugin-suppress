# fluent-plugin-suppress

[![Build Status](https://travis-ci.org/fujiwara/fluent-plugin-suppress.svg?branch=master)](https://travis-ci.org/fujiwara/fluent-plugin-suppress)

## SuppressOutout / SuppressFilter

Fluentd plugin to suppress same messages.

## Configuration

fluentd.conf

```
<match foo.**>
  type           suppress
  interval       10
  num            2
  attr_keys      host,message
  add_tag_prefix sp.
</match>
```

In `interval` sec, `num` messages which grouped by `attr_keys` value, add tag prefix "`sp.`" and pass to follow process. Other messages will be removed.

Input messages:

```
  2012-11-22T11:22:33 foo.info {"id":1,"host":"web01","message":"error!!"}
  2012-11-22T11:22:34 foo.info {"id":2,"host":"web01","message":"error!!"}
* 2012-11-22T11:22:35 foo.info {"id":3,"host":"web01","message":"error!!"}
* 2012-11-22T11:22:36 foo.info {"id":4,"host":"web01","message":"error!!"}
  2012-11-22T11:22:37 foo.info {"id":5,"host":"app01","message":"error!!"}
* 2012-11-22T11:22:38 foo.info {"id":6,"host":"web01","message":"error!!"}
* 2012-11-22T11:22:39 foo.info {"id":7,"host":"web01","message":"error!!"}
* 2012-11-22T11:22:40 foo.info {"id":8,"host":"web01","message":"error!!"}
  2012-11-22T11:22:45 foo.info {"id":9,"host":"web01","message":"error!!"}
```
(* = suppressed)

Output messages:

```
 2012-11-22T11:22:33 sp.foo.info {"id":1,"host":"web01","message":"error!!"}
 2012-11-22T11:22:34 sp.foo.info {"id":2,"host":"web01","message":"error!!"}
 2012-11-22T11:22:37 sp.foo.info {"id":5,"host":"app01","message":"error!!"}
 2012-11-22T11:22:45 sp.foo.info {"id":9,"host":"web01","message":"error!!"}
```

### Filter plugin

Fluentd >= v0.12 can use filter plugin.

```
<filter foo.**>
  type      suppress
  interval  10
  num       2
  attr_keys host,message
</filter>
```

Filter plugin will not replace a tag.

### Group by nested key name

`attr_keys` allows nested key name (eg. `foo.bar`).

```
<match foo.**>
  type           suppress
  attr_keys      data.host, data.message
</match>
```

Input messages will be suppressed by key data.host and data.message.

```
 2012-11-22T11:22:33 foo.info {"id":1,"data":{"host":"web01","message":"error!!"}}
 2012-11-22T11:22:34 foo.info {"id":2,"data":{"host":"web01","message":"error!!"}}
```

### Suppressing by tag only

If `attr_keys` is not specified, records will be suppressed by tag only.

```
<match foo.**>
  type  suppress
  ...
</match>
```

## TODO

patches welcome!

## Copyright

Copyright (c) 2012- FUJIWARA Shunichiro
License   Apache License, Version 2.0
