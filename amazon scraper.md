from timeit import default_timer as timer
# time
start = timer()

import csv
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.edge.options import Options
import time




class crawledArticle():
    def __init__(self, title, price):
        self.title = title
        # self.rating=rating
        self.price = price


class b:

    def article(self, name):
        count = 1
        page = 1
        pageIncrement = 5
        maxRetrieves = 100

        a = []
        url = "https://www.amazon.com/s?k=" + name + "&page=" + str(page)

        options = Options()
        options.headless = False
        #options.add_experimental_option("detach", True)
        browser = webdriver.Edge(executable_path="D:\\me\\web automation\\msedgedriver.exe")
        browser.maximize_window()
        browser.get(url)
        browser.set_page_load_timeout(10)

        while True:
            try:
                if pageIncrement * page > maxRetrieves:
                    break

                if count > pageIncrement:
                    count = 1
                    page += 1

                # Get Title
                #            '//*[@id="search"]/div[1]/div[1]/div/span[3]/div[2]/div[2]                  /div/span/div/div/div[2]/div[2]/div/div/div[1]/h2/a'
                #            '//*[@id="search"]/div[1]/div[1]/div/span[3]/div[2]/div[1                 ]/div/span/div/div/div[2]/div[2]/div/div/div[1]/h2/a/span'

                xPathTitle = '//*[@id="search"]/div[1]/div[1]/div/span[3]/div[2]/div' + str([count]) +'/div/span/div/div/div[2]/div[2]/div/div/div[1]/h2/a'
                print (xPathTitle)
                time.sleep(3)
                title = browser.find_element_by_xpath(xPathTitle)
                titleText = title.get_attribute("innerHTML").splitlines()[0]
                title.click()

                # Get Price
                xPathPrice = '//*[@id="priceblock_ourprice"]'
                price = browser.find_element_by_xpath(xPathPrice)
                priceText = price.get_attribute("innerHTML")

                info = crawledArticle(titleText, priceText)
                a.append(info)

                url = "https://www.amazon.com/s?k="+str(name)+"&page=" + str(page)
                browser.get(url)
                browser.set_page_load_timeout(10)



                count += 1
                print (f'sucsess' , count)

            except Exception as e:
                print("Exception On Count", count, e)
                count += 1
                print (f'fail', count)

                if pageIncrement * page > maxRetrieves:
                    break

                if count > pageIncrement:
                    count = 1
                    page += 1

                url = "https://www.amazon.com/s?k=iphone+11&page=" + str(page)
                browser.get(url)
                browser.set_page_load_timeout(10)

        browser.close()

        return a


fetcher = b()

with open('pro.csv', 'w', newline='', encoding='utf-8') as csvfile:
    articlewriter = csv.writer(csvfile, delimiter=';', quotechar='"', quoting=csv.QUOTE_MINIMAL)
    for article in fetcher.article("iphone 11"):
        articlewriter.writerow([article.title, article.price])

# measure elapsed time
end = timer()
print ("elapsed time (s) :", end - start)
