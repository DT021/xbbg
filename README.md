# xbbg

[![PyPI version](https://badge.fury.io/py/xbbg.svg)](https://badge.fury.io/py/xbbg)
[![Documentation Status](https://readthedocs.org/projects/xbbg/badge/?version=latest)](https://xbbg.readthedocs.io/en/latest)

Bloomberg data toolkit for humans

## Requirements

- Bloomberg Open API

```
pip install --index-url=https://bloomberg.bintray.com/pip/simple blpapi
```

- [pdbdp](https://github.com/matthewgilbert/pdblp) - pandas wrapper for Bloomberg Open API
- numpy, pandas, pyarrow, [xone](https://github.com/alpha-xone/xone), etc.

## Installation

```
pip install xbbg
```

## Tutorial

Creation of connection `create_connection()` is not necessary.
Quries will create new connections if there's no live connections on the backend.
Since each initiation of connection takes time, we can manually connect
before we want to do multiple queries - just like examples below.

```python
In[1]: from xbbg import blp, conn

In[2]: conn.create_connection()
```

```
Out[2]: (<pdblp.pdblp.BCon at 0x1c35cd0e898>, True)
```

- ``BDP`` example:

```python
In[3]: blp.bdp(tickers='NVDA US Equity', flds=['Security_Name', 'GICS_Sector_Name'])
```

```
Out[3]:
           ticker             field                   value
0  NVDA US Equity     Security_Name             NVIDIA Corp
1  NVDA US Equity  GICS_Sector_Name  Information Technology
```

- ``BDH`` example:

```python
In[4]: blp.bdh(
  ...:     tickers='SPX Index', flds=['High', 'Low', 'Last_Price'],
  ...:     start_date='2018-10-10', end_date='2018-10-20',
  ...: )
```

```
Out[4]:
ticker     SPX Index
field           High      Low Last_Price
date
2018-10-10  2,874.02 2,784.86   2,785.68
2018-10-11  2,795.14 2,710.51   2,728.37
2018-10-12  2,775.77 2,729.44   2,767.13
2018-10-15  2,775.99 2,749.03   2,750.79
2018-10-16  2,813.46 2,766.91   2,809.92
2018-10-17  2,816.94 2,781.81   2,809.21
2018-10-18  2,806.04 2,755.18   2,768.78
2018-10-19  2,797.77 2,760.27   2,767.78
```

- ``BDH`` example with Excel compatible inputs:

```python
In[4]: blp.bdh(
  ...:     tickers='SHCOMP Index', flds=['High', 'Low', 'Last_Price'],
  ...:     start_date='2018-09-26', end_date='2018-10-20',
  ...:     Per='W', Fill='P', Days='A',
  ...: )
```

```
Out[4]:
ticker     SHCOMP Index
field              High      Low Last_Price
date
2018-09-28     2,827.34 2,771.16   2,821.35
2018-10-05     2,827.34 2,771.16   2,821.35
2018-10-12     2,771.94 2,536.66   2,606.91
2018-10-19     2,611.97 2,449.20   2,550.47
```

- ``BDS`` example

```python
In[5]: blp.bds('AAPL US Equity', 'DVD_Hist_All', DVD_Start_Dt='20180101', DVD_End_Dt='20180531')
```

```
Out[5]:
            ticker         field                name         value  position
0   AAPL US Equity  DVD_Hist_All       Declared Date    2018-05-01         0
1   AAPL US Equity  DVD_Hist_All             Ex-Date    2018-05-11         0
2   AAPL US Equity  DVD_Hist_All         Record Date    2018-05-14         0
3   AAPL US Equity  DVD_Hist_All        Payable Date    2018-05-17         0
4   AAPL US Equity  DVD_Hist_All     Dividend Amount          0.73         0
5   AAPL US Equity  DVD_Hist_All  Dividend Frequency       Quarter         0
6   AAPL US Equity  DVD_Hist_All       Dividend Type  Regular Cash         0
7   AAPL US Equity  DVD_Hist_All       Declared Date    2018-02-01         1
8   AAPL US Equity  DVD_Hist_All             Ex-Date    2018-02-09         1
9   AAPL US Equity  DVD_Hist_All         Record Date    2018-02-12         1
10  AAPL US Equity  DVD_Hist_All        Payable Date    2018-02-15         1
11  AAPL US Equity  DVD_Hist_All     Dividend Amount          0.63         1
12  AAPL US Equity  DVD_Hist_All  Dividend Frequency       Quarter         1
13  AAPL US Equity  DVD_Hist_All       Dividend Type  Regular Cash         1
```

- Intraday bars ``BDIB`` example:

```python
In[6]: blp.bdib(ticker='BHP AU Equity', dt='2018-10-17').tail()
```

```
Out[6]:
                           open  high   low  close   volume  numEvents
2018-10-17 15:56:00+11:00 33.62 33.65 33.62  33.64    16660        126
2018-10-17 15:57:00+11:00 33.65 33.65 33.63  33.64    13875        156
2018-10-17 15:58:00+11:00 33.64 33.65 33.62  33.63    16244        159
2018-10-17 15:59:00+11:00 33.63 33.63 33.61  33.62    16507        167
2018-10-17 16:10:00+11:00 33.66 33.66 33.66  33.66  1115523        216
```

- Intraday bars within market session:

```python
In[7]: blp.intraday(ticker='7974 JT Equity', dt='2018-10-17', session='am_open_30').tail()
```

```
Out[7]:
                               open      high       low     close  volume  numEvents
2018-10-17 09:27:00+09:00 39,970.00 40,020.00 39,970.00 39,990.00   10800         44
2018-10-17 09:28:00+09:00 39,990.00 40,020.00 39,980.00 39,980.00    6300         33
2018-10-17 09:29:00+09:00 39,970.00 40,000.00 39,960.00 39,970.00    3300         21
2018-10-17 09:30:00+09:00 39,960.00 40,010.00 39,950.00 40,000.00    3100         19
2018-10-17 09:31:00+09:00 39,990.00 40,000.00 39,980.00 39,990.00    2000         15
```

- Earnings

```python
In[8]: blp.earning('AMD US Equity', Eqy_Fund_Year=2017, Number_Of_Periods=1)
```

```
Out[8]:

                 Level   FY_2017  FY_2017_Pct
Asia-Pacific      1.00  3,540.00        66.43
    China         2.00  1,747.00        49.35
    Japan         2.00  1,242.00        35.08
    Singapore     2.00    551.00        15.56
United States     1.00  1,364.00        25.60
Europe            1.00    263.00         4.94
Other Countries   1.00    162.00         3.04
```

- Dividends

```python
In[9]: blp.dividend(['C US Equity', 'MS US Equity'], start_date='2018-01-01', end_date='2018-05-01')
```

```
Out[9]:
               declared_date     ex_date record_date payable_date dividend_amount dividend_frequency dividend_type
ticker
C US Equity       2018-01-18  2018-02-02  2018-02-05   2018-02-23            0.32            Quarter  Regular Cash
MS US Equity      2018-04-18  2018-04-27  2018-04-30   2018-05-15            0.25            Quarter  Regular Cash
MS US Equity      2018-01-18  2018-01-30  2018-01-31   2018-02-15            0.25            Quarter  Regular Cash
```

## Optimizations

This library uses a global Bloomberg connection on the backend - 
more specically, `_xcon_` in `globals()` variable.
Since initiation of connections takes time, if multiple queries are expected,
manually create a new connection (which will be shared by all following queries)
is helpful before calling any queries:

```python
from xbbg import conn

conn.create_connection()
```

## Data Storage

If `BBG_ROOT` is provided in `os.environ`, data can be saved locally.
By default, local storage is preferred than Bloomberg for all queries.

Noted that local data usage must be compliant with Bloomberg Datafeed Addendum
(full description in `DAPI<GO>`):

> To access Bloomberg data via the API (and use that data in Microsoft Excel), 
> your company must sign the 'Datafeed Addendum' to the Bloomberg Agreement. 
> This legally binding contract describes the terms and conditions of your use 
> of the data and information available via the API (the "Data"). 
> The most fundamental requirement regarding your use of Data is that it cannot 
> leave the local PC you use to access the BLOOMBERG PROFESSIONAL service.

## Bloomberg Binaries

- Reference: https://www.bloomberg.com/professional/support/api-library/
- Install with `pip`

```
python -m pip install --index-url=https://bloomberg.bintray.com/pip/simple blpapi
```

- Install in `pipenv`

Add `source` in `Pipfile`

```toml
[[source]]
name = "bbg"
url = "https://bloomberg.bintray.com/pip/simple"
verify_ssl = true
```

Run `pipenv`

```
cd ...\xbbg\env
pipenv install blpapi
```
