## ZoomEye Python

`ZoomEye` 是一款网络空间搜索引擎，可让用户查找特定的网络组件。 `ZoomEye API` 是 `ZoomEye` 提供的 API 接口，通过 `HTTPS` 查询数据，方便用户在自己的应用中使用 ZoomEye 。

`ZoomEye Python` 是一款基于 `ZoomEye API` 开发的命令行工具，同时也是集成了 `ZoomEye SDK` 的 Python 库。

支持版本：**Python3+**

### 0x01 How to install?

```bash
$ pip3 install zoomeye-python3
```

or

```bash
$ pip3 install git+https://github.com/knownsec/zoomeye-python3.git
```

### 0x02 How to use?

#### SDK

##### 1. 初始化

首先初始化，初始化支持**用户名和密码**以及 **API KEY** 两种方式。

**用户名和密码**

```python
from zoomeye.sdk import ZoomEye

zm = ZoomEye(username="usernmae", password="password")
```

**API KEY**

```python
from zoomeye.sdk import ZoomEye

zm = ZoomEye(api_key="6*****-7**B-4****6-9e8f-a********4")
```

##### 2. dork_search()

使用 **dork_search()** 搜索单页数据。该函数通过请求 API 得到结果，SDK  返回的结果是一个列表，当然这是经过处理的。如果你想获得 API 返回的原始数据可以 通过**raw_data** 获得。

```python
from zoomeye.sdk import ZoomEye

zm = ZoomEye(username="usernmae", password="password")
zm.login()
print(zm.dork_search("apache"))

# [{'protocol': {'application': 'HTTP', 'probe': 'GetRequest', 'transport': 'tcp'}, 'ip': '50.***.126.**', 'timestamp': '2020-12-15T16:02:30', 'geoinfo': {'organization_CN': None, # 'aso': None, 'continent': {'geoname_id': None,
```

**()** 提供一个必填参数和三个可选参数：

- 必填参数
  - dork：需要搜索的 **dork**
- 选填参数
  - page : 获取数据的页数，默认获取第一页的数据。
  - resource : 搜索过滤器，提供 **主机设备搜索过滤器 (host)** 和 **Web 应用搜索过滤器 (Web)**，默认为 **host**
  - facets :统计项目，可以为多个，默认不统计。

统计项目支持字段：

| 统计项  |     说明     |
| :-----: | :----------: |
|   app   | 应用，设备等 |
| device  |   设备类型   |
| service |   服务类型   |
|   os    |   操作系统   |
|  port   |    端口号    |
| country |     国家     |
|  city   |     城市     |

##### 3. multi_page_search()

**multi_page_search ()** 是 **dork_search ()** 的拓展版本可以一次性返回多页的数据。与 **dork_search()** 的唯一的区别在于可选参数 `page` 上，在 **multi_page_search()** 中表示的是个多少页数据，如：`page=5` 时，则函数返回的是 5 x 20 条数据 **(ps : 每次查询时数据都会发送新的 API 请求，因此 page 的值越大获取值的时间越长)**。

##### 4. dork_filter()

使用 **dork_filter()** 对返回的结果进行过滤。

```python
from zoomeye.sdk import ZoomEye

zm = ZoomEye(username="username", password="password")
zm.login()
zm.dork_search("apache")
print(zm.dork_filter("ip,port,app"))

# [['***.***.27.2**', 80, 'Apache httpd'], ['***.2**.205.**4', 80, 'Apache httpd'],...]
```

由于 **主机设备** 和 **Web 应用** 的字段不尽相同，在使用 **dork_filter()** 时需根据搜索的设备进行过滤。

**主机设备 (host)** 目前该函数支持字段的有:

|    字段    | 描述              |
| :--------: | ----------------- |
|    app     | 应用，产品        |
|  version   | 版本              |
|   device   | 设备类型          |
|     ip     | IP 地址           |
|    port    | 端口号            |
|  hostname  | 主机名称          |
|    city    | 城市名称(English) |
|  city_cn   | 城市名称(中文)    |
|  country   | 国家名称(English) |
| country_cn | 国家名称(中文)    |
|    asn     | ASN 号码          |
|   banner   | 端口指纹信息      |

**Web设备 (web)** 支持的字段有：

|    字段    |        描述         |
| :--------: | :-----------------: |
|    app     |    Web 应用信息     |
|  headers   |    HTTP headers     |
|  keywords  |   meta 属性关键词   |
|   title    | HTTP Title 标题信息 |
|     ip     |       IP 地址       |
|    site    |      site 搜索      |
|    city    | 城市名称（English） |
|  city_cn   |  城市名称（中文）   |
|  country   | 国家名称（English） |
| country_cn |  国家名称（中文）   |

##### 5. user_info()

- desc: 获取用户信息。
- return: dict

##### 6. show_count()

- desc: 统计当前搜素 dork 在 ZoomEye 的总量。
- retrun: int

##### 7. get_facet()

- desc: 获取统计项目的数据。
- return: dict

##### 8. show_site_ip()

- desc : 指定过滤器为  **Web 应用搜索过滤器 (Web)** 时获取 domain name 和 IP

##### 9. show_ip_port()

- desc : 指定过滤器为  **主机设备搜索过滤器 (host)** 时获取 IP 和 prot



#### command tool

在安装新版的 ZoomEye  可以直接使用 `zoomeye` 命令，命令行提供下列功能。

```bash
$ zoomeye -h     
usage: zoomeye [-h] {info,dork,init} ...

positional arguments:
  {info,dork,init}
    info            show user account info
    dork            search information from zoomeye api by dork
    init            please input username and password or api key!

optional arguments:
  -h, --help        show this help message and exit
```

##### 1. init command

```bash
$ zoomeye init -h                                              
usage: zoomeye init [-h] [-apikey [api key]] [-username [username]]
                    [-password [password]]

optional arguments:
  -h, --help            show this help message and exit
  -apikey [api key]     api key
  -username [username]  zoomeye account username
  -password [password]  zoomeye account password

```

`init` 命令是初始化配置命令，可以选择使用 **API KEY** 或者 **用户名 & 密码** 进行初始化。

**在这里推荐使用 API KEY 进行初始化**

通过 **用户名 & 密码** 的配置具有一定的时效性，即：在认证数据失效后需要重新运行此命令，才能正常使用，而使用 **API KEY** 进行初始化就不会出现该问题。

> API KEY 可以在 [https://www.zoomeye.org/profile](https://www.zoomeye.org/profile) 获得。

##### 2. dork command

`dork` 命令用于搜索数据，过滤，筛选数据。

```bash
$ zoomeye dork -h                                    
usage: cli.py dork [-h] [-num [value]] [-facet [field]]
                   [-filter [field=regexp]] [-stat [field]]
                   [-save [field=regexp]] [-count]
                   dork

positional arguments:
  dork                  please input zoomeye dork or zoomeye export file

optional arguments:
  -h, --help            show this help message and exit
  -num [value]          set the number of obtained data
  -facet [field]        perform statistics on all data by field under this
                        dork, field: [app,device,service,os,port,country,city]
  -filter [field=regexp]
                        output more clearer data results by set filter field,
                        field:
                        [app,version,device,port,city,country,asn,banner,*]
  -stat [field]         statistics on the data results of the query, field:
                        [app,device,service,os,port,country,city]
  -save [field=regexp]  save the data with ZoomEye json format, if you specify
                        the field (like filter), it will be saved in the line
                        json format
  -count                the total amount of dork data in ZoomEye database
```

必填参数：

- dork : ZoomEye 搜索的 dork 或者通过 zoomeye dork 命令导出的文件

可选参数：

- -num : 获取数据的数量，如 `-num 30` ，则返回 30 条数据。（由于 API 每次返回的数据为 20 条，因此在进行转换时进行取证，即：当输入的数量为不是 20 的整数倍，在请求数据时会强制变成 20 的整数倍。对于多的数据会保存在本地缓存中）**推荐在指定数据量时，数据量是 20 的整数倍**。
- -facet : 对指定字段进行统计，如 `-facet app, device`，默认为 : `app` 
- -filter  [field = regexp]:  对数据进行筛选，其中 regexp 是可选的；当未指定 regexp 时，结果包含该 field，当指定 regexp 时，将按照该值对内容进行过滤，并在结果包含该 field、vaule ，默认为 : `app` 
- -stat : 对当前的数据进行数据聚合，如:  当 `-num 30` 时，`-stat os,port` 则是对 30 条数据的聚合 ，默认为 : `app` 
- -save : 默认情况下，会保存 ZoomEye API 的原始数据；用户指定 field (和 filter 语法一致)，将按 line json格式进行保存。如: `-save app=xxx` 

**facet 支持字段 ：**

| 统计项  |     说明     |
| :-----: | :----------: |
|   app   | 应用，设备等 |
| device  |   设备类型   |
| service |   服务类型   |
|   os    |   操作系统   |
|  port   |    端口号    |
| country |     国家     |
|  city   |     城市     |

**filter支持字段 :**

|  字段   | 描述              |
| :-----: | ----------------- |
|   app   | 应用，产品        |
| version | 版本              |
| device  | 设备类型          |
|   ip    | IP 地址           |
|  port   | 端口号            |
|  city   | 城市名称(English) |
| country | 国家名称(English) |
|   asn   | ASN 号码          |
| banner  | 端口指纹信息      |

##### 3. info command

`info` 命令获取用户信息，包括角色，剩余配额。

```bash
zoomeye info -h
usage: zoomeye info [-h]

optional arguments:
  -h, --help  show this help message and exit
```

### 0x03 Video

[![asciicast](https://asciinema.org/a/lCyhndvaRAsi7fZjLpqcDBVKr.svg)](https://asciinema.org/a/lCyhndvaRAsi7fZjLpqcDBVKr)

### 0x04 contribution

[r0oike@knownsec 404](https://github.com/r0oike)

[0x7F@knownsec 404](https://github.com/0x7Fancy)

[fenix@knownsec 404](https://github.com/13ph03nix)

[dawu@knownsec 404](https://github.com/d4wu)

### 0x05 issue

**1.SDK和命令行工具的最小请求数量为 20 条**

由于 API 的限制导致我们的查询最小单位一次为 20 条数据，对于一个新的 dork 来讲，无论是查看总数量，还是指定只搜索 1 条数据，都将会产生 20 条的开销；当然在命令行模式下我们提供了缓存机制，对于已经搜索过的数据缓存到本地(`~/.config/zoomeye/cache`)，有效期为 5 天，可以大幅度的节省配额。

**2. 为什么在 facet 会出现数据会不一致？**

如图所示

![image-20210111111035187](./images/image-20210111111035187.png)

以上进行 "telnet" 的全数据统计结果，第一次查询的结果是一天前由命令行工具默认发起 20 条数据的查询请求(其中包含统计结果)，并缓存至本地文件夹中；第二次查询我们设置数量为 21 条，命令行工具将读取缓存的 20 条数据，并发起新的查询请求 1 条(实际为最小单位 20 条，其中也包含统计结果)，第一次查询和第二次查询中间间隔一定的时间，这段时间间隔内由于 ZoomEye 周期性的扫描可能更新了该数据，导致出现了如上的数据不一致的情况，因此命令行工具将以新的统计结果为准。

**3.为什么 ZoomEye command tool 和 ZoomEye Web 搜索同一个 dork 数据总量可能会不一样？**

![image-20210111141028072](./images/image-20210111141028072.png)

![image-20210111141114558](./images/image-20210111141114558.png)

ZoomEye 提供了两个搜索接口分别是 :  `/host/search` 和 `/web/search` ，在 ZoomEye command tool 中只使用了 `/host/search` 。在多数情况下，host 接口所提供的数据可以覆盖 90% 甚至 99% 的数据，因此数据的准确性是可以保证的，而在 API 进行请求时，将消耗用户配额，如果要兼容两种接口的话，将会更多的消耗用户配额；因此在命令行工具中，只使用了 `/host/search` 接口进行搜索。

