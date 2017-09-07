# Stock_history

import re
import requests
import json


def stock_list():
    fhandle = requests.get('http://money.cnn.com/data/dow30/')
    search_pattern = re.compile('class="wsod_symbol">(.*?)<\/a>.*<span.*">(.*?)<\/span>.*\n.*class="wsod_stream">(.*?)<\/span>')
    stock_list_in_text = re.findall(search_pattern,fhandle.text)
    stk_list = []
    for item in stock_list_in_text():
        stk_list.append({ 'code' : item[0], 'name' : item[1], 'price' : float(item[2]) })
    return stk_list

def stock_data(stock_code, start = '', end = ''):
    json_data = []
    url = 'https://finance.yahoo.com/quote/%s/history?p=%s' % (stock_code,stock_code)
    fh = requests.get(url)
    str_data =  re.findall('"HistoricalPriceStore":{"prices":(.*),"isPending"',fh.text)
    if str_data:
        json_data = json.loads(str_data[0])
        json_data = json_data[::-1]
    return [item for item in json_data if not 'type' in item]
