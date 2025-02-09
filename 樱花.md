import time
import requests
from lxml import etree
import urllib.parse


def encode_text(text):
    # 使用 urllib.parse.quote 进行 URL 编码
    encoded_text = urllib.parse.quote(text, encoding='utf-8')
    return encoded_text
def url_list(region_encode_text,page_count,headers):
    for page in range(0, page_count ):
        url_page = f'https://www.yhmgo.com/list/?region={region_encode_text}&pagesize=24&pageindex={page}'
        url_request(url_page, headers)
def url_request(url, headers):
    #请求链接
    try:
        res = requests.get(url=url, headers=headers)
        res.raise_for_status()
        page = res.content.decode()
        html = etree.HTML(page)
        get_anime_url(html)
    except requests.exceptions.RequestException as e:
        print(f"Error fetching {url}: {e}")


def get_anime_url(html):
    #获取动漫链接
    url_list = html.xpath('//div[@class="fire l"]/div[@class="lpic"]/ul/li')
    for item in url_list:
        dm_titles = item.xpath('.//a/img/@alt')
        dm_urls = item.xpath('.//a/@href')
        if dm_titles and dm_urls:
            dm_title = dm_titles[0]
            dm_url = dm_urls[0]
            print(f"名称: {dm_title} 网址: https://www.yhmgo.com{dm_url}")
            print("=============================")
        else:
            print("No matching elements found")


if __name__ == '__main__':
    print("请输入想查找动漫的地区")
    region = input()
    region_encode_text = encode_text(region)
    print("请输入想查找的页数")
    page_count = int(input())
    headers = {
        "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36",
    }
    url_list(region_encode_text,page_count, headers)

