# crawl-web
#This code includes crawling web pages
import requests
from bs4 import BeautifulSoup
from newspaper import Article
from tqdm import tqdm
import pandas as pd

# crawling in technolife.ir/blog 
def scarab_page():
    page = 1
    productLink =[]
    scrapdata = []

    while(True):      
        page +=1
        main_url = f'https://www.technolife.ir/blog/page/{page}/'
        html = requests.get(main_url).text
        soup = BeautifulSoup(html , features = 'lxml')

        # Stop while
        if( soup.find('h2' , class_='error-title')):
            print(' page is end.....')
            break

        # if in div class="blog-entry-readmore clr" we can find url_pages
        links = soup.find_all('div' , class_='blog-entry-readmore clr')
        print(f"load page {page}")
        for link in tqdm(links):
            # find url_page
            page_url = link.a['href']
            productLink.append(page_url)

            # we use Article package to extract title and text pages
            try:
                article = Article(page_url)
                article.download() # download pages
                article.parse()
                scrapdata.append({'url': page_url , 'title': article.title , 'text': article.text})
            except:
                print(f' Failed to page: {page_url}')    
    
        
    # save information pages to format csv
    df = pd.DataFrame(scrapdata)
    df.to_csv('technolife_blog.csv')
    

if __name__== '__main__':
    scarab_page() 
