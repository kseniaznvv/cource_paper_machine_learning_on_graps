# cource_paper_machine_learning_on_graps
import requests
from bs4 import BeautifulSoup

url = 'https://checko.ru' 


response = requests.get(url)
html_content = response.text

soup = BeautifulSoup(html_content, 'html.parser')

import pandas as pd
import re

df=pd.read_excel('okved (1).xlsx', skiprows=[0, 1, 2, 3, 4, 5, 2908, 2907, 2906, 2905, 2904, 2903, 2902, 2901, 2900, 2899])
okved=df['Код']
okved

def clean(s):
    kod=re.sub(r'[^\w\s]', '', str(s))
    return kod
    

okv=[]
okved=list(okved.apply(lambda x: clean(x)))
for i in okved:
    if len(i)<6:
        i=i+'0'*(6-len(i))
    okv.append(i)



for o in all_links_no_pages:
    response = requests.get(o)
    html_content = response.text
    soup = BeautifulSoup(html_content, 'html.parser')
    l=[i.text for i in soup.find_all('li') if (i.text.isdigit())==True]
    if len(l)>0:
        for page in range(2, int(l[-1])+1):
            links_pages.append(o+f'&page={page}')
#     org_block = soup.find_all('a', class_="link")
#     names_company=[i['href'] for i in org_block]
    print(len(links_pages))
    
 pd.DataFrame(all_links).iloc[0:60000].to_excel('Ссылки на страницы активные организации 1.xlsx')

pd.DataFrame(all_links).iloc[60000:].to_excel('Ссылки на страницы активные организации 2.xlsx')

def companies_from_pages(url):
    response = requests.get(url)
    html_content = response.text
    soup = BeautifulSoup(html_content, 'html.parser')
    org_block = soup.find_all('a', class_="link")
    # print(org_block)
    names=[i['href'] for i in org_block]
    more_links_=['https://checko.ru/'+i for i in names if 'select' not in i]
    return more_links_

# links_1[1]=links_1[0].apply(companies_from_pages)

%%time
first_200=links_1[0][:200].apply(companies_from_pages) # 7 секунд для 10 страниц --> 11 часов

first_200.to_excel('Первые 200 страниц с организациями.xlsx')



a=[]
def read_list(page):
    for i in page:
        a.append(i)
    return a
    

a.extend(first_200.map(read_list))


pd.DataFrame(a).to_excel('Первые 5100 организаций.xlsx')

def get_inn(url):
#     print(url)
    requisites = []
    if 'person' in url:
        return url[-12:]
    else:
        response = requests.get(url)
        soup = BeautifulSoup(response.content, "html.parser")
        requisites_table = soup.find('table', class_='uk-table-striped')
        if requisites_table is None:
            pass
        else:
            for row in requisites_table.find_all('tr'):
                cells = row.find_all('td')
                requisites.append(cells[0].get_text(strip=True))
            if len(requisites)>3: 
                inn = requisites[1]
                return inn
            else: pass
            
def all_parse(url):
    print(url)
    response = requests.get(url)
    soup = BeautifulSoup(response.content, "html.parser")
    company_information = []
    inf = soup.find_all('div', class_='basic-data')
    for i in inf:
        company_information.append(i.text.replace('\xa0', ' ').replace('\n', ' ').replace('\u2009', ''))

    contacts_stat = {}
    owner = []

    requisites_table = soup.find_all('section', class_='x-section')[7]
    for row in requisites_table.find_all('div'):
        owner.append(row.get_text(' ', strip=True))

    requisites = []

    requisites_table = soup.find('table', class_='uk-table-striped')
    for row in requisites_table.find_all('tr'):
        cells = row.find_all('td')
        requisites.append(cells[0].get_text(strip=True))
    if len(requisites) > 3:
        ogrn = requisites[0]
        inn = requisites[1]
        kpp = requisites[2]
        okpo = requisites[3]
    main_inn=inn

    contacts_stat = {}
    connect = []
    all_connect = []
    connectl = []
    connectfs = []
    connectm = []
    connectfd = []
    all_connectfd = []
    all_connectl = []
    all_connectm = []
    all_connectfs = []

    if len(soup.find_all('div', class_='more-link')) > 2:
        requisites_table = soup.find_all('div', class_='more-link')[2]
        for row in requisites_table.find_all('a', class_='link'):
            a = '='.join(row['href'].split('=')[:-1])

            connectl.append('https://checko.ru/' + a + '=leaders')
            connectfs.append('https://checko.ru/' + a + '=founders')
            connectm.append('https://checko.ru/' + a + '=managed')
            connectfd.append('https://checko.ru/' + a + '=founded')

        for u in connectl:
            if 'connections' in u:
                response = requests.get(u)
                soup = BeautifulSoup(response.content, "html.parser")
                l = [i.text for i in soup.find_all('li') if (i.text.isdigit()) == True]
                for i in soup.find_all('td'):
                    vk = i.find_all('a', class_='link')
                    if len(vk) == 2:
                        v = 'https://checko.ru/' + vk[0]['href']
                        k = 'https://checko.ru/' + vk[1]['href']
                        vinn = get_inn(v)
                        if vinn != None and vinn != main_inn:
                            all_connectl.append({get_inn(k): vinn})

                for page in range(2, len(l) + 1):
                    u = u + f'&page={page}'
                    response = requests.get(u)
                    soup = BeautifulSoup(response.content, "html.parser")
                    for i in soup.find_all('td'):
                        vk = i.find_all('a', class_='link')
                        if len(vk) == 2:
                            v = 'https://checko.ru/' + vk[0]['href']
                            k = 'https://checko.ru/' + vk[1]['href']
                            vinn = get_inn(v)
                            if vinn != None and vinn != main_inn:
                                all_connectl.append({get_inn(k): vinn})

        for u in connectfs:
            if 'connections' in u:
                response = requests.get(u)
                soup = BeautifulSoup(response.content, "html.parser")
                l = [i.text for i in soup.find_all('li') if (i.text.isdigit()) == True]
                for i in soup.find_all('td'):
                    vk = i.find_all('a', class_='link')
                    if len(vk) == 2:
                        v = 'https://checko.ru/' + vk[0]['href']
                        k = 'https://checko.ru/' + vk[1]['href']
                        vinn = get_inn(v)
                        if vinn != None and vinn != main_inn:
                            all_connectfs.append({get_inn(k): vinn})

                for page in range(2, len(l) + 1):
                    u = u + f'&page={page}'
                    response = requests.get(u)
                    soup = BeautifulSoup(response.content, "html.parser")
                    for i in soup.find_all('td'):
                        vk = i.find_all('a', class_='link')
                        if len(vk) == 2:
                            v = 'https://checko.ru/' + vk[0]['href']
                            k = 'https://checko.ru/' + vk[1]['href']
                            vinn = get_inn(v)
                            if i != None and vinn != main_inn:
                                all_connectfs.append({get_inn(k): vinn})

        for u in connectfd:
            if 'connections' in u:
                response = requests.get(u)
                soup = BeautifulSoup(response.content, "html.parser")
                l = [i.text for i in soup.find_all('li') if (i.text.isdigit()) == True]
                for c in soup.find_all('a', class_='link')[1:]:
                    if '/company/' in c['href'] and 'select' not in c['href'] and c['href'][
                                                                                  -13:] not in all_connectfd and 'extra' not in \
                            c['href']:
                        i = get_inn('https://checko.ru/' + c['href'])
                        if i != None and i != main_inn:
                            all_connectfd.append(i)
                for page in range(2, len(l) + 1):
                    u = u + f'&page={page}'
                    response = requests.get(u)
                    soup = BeautifulSoup(response.content, "html.parser")
                    l = [i.text for i in soup.find_all('li') if (i.text.isdigit()) == True]
                    for c in soup.find_all('a', class_='link')[1:]:
                        if '/company/' in c['href'] and 'select' not in c['href'] and 'extra' not in c['href'] and c[
                                                                                                                       'href'][
                                                                                                                   -13:] not in all_connectfd:
                            i = get_inn('https://checko.ru/' + c['href'])
                            if i != None and i != main_inn:
                                all_connectfd.append(i)

        for u in connectm:
            if 'connections' in u:
                response = requests.get(u)
                soup = BeautifulSoup(response.content, "html.parser")
                l = [i.text for i in soup.find_all('li') if (i.text.isdigit()) == True]
                for c in soup.find_all('a', class_='link')[1:]:
                    if '/company/' in c['href'] and 'select' not in c['href'] and c['href'][
                                                                                  -13:] not in all_connectm and 'extra' not in \
                            c['href']:
                        print('https://checko.ru/' + c['href'])
                        i = get_inn('https://checko.ru/' + c['href'])
                        if i != None and i!=main_inn:
                            all_connectm.append(i)
                for page in range(2, len(l) + 1):
                    u = u + f'&page={page}'
                    response = requests.get(u)
                    soup = BeautifulSoup(response.content, "html.parser")
                    l = [i.text for i in soup.find_all('li') if (i.text.isdigit()) == True]
                    for c in soup.find_all('a', class_='link')[1:]:
                        if '/company/' in c['href'] and 'select' not in c['href'] and 'extra' not in c['href'] and c[
                                                                                                                       'href'][
                                                                                                                   -13:] not in all_connectm:
                            i = get_inn('https://checko.ru/' + c['href'])
                            if i != None and i != main_inn:
                                all_connectm.append(i)


    else:

        for i in soup.find_all('div', class_='connections'):

            for row in i.find_all('a', class_='link'):

                if 'company' in row['href']:
                    connect.append('https://checko.ru/' + row['href'])

        for u in connect:
            i = get_inn(u)
            if i not in all_connect:
                if i != None and i != main_inn:
                    all_connect.append(i)

    return owner, ogrn[:13], inn, kpp, okpo, all_connectl, all_connectfs, all_connectm, all_connectfd, all_connect


first10160org=pd.read_excel('Первые 5100 организаций.xlsx', index_col=0)
first10160org=first10160org.head(9960)
# first10160org.head(50)

%%time
first10160org[['Владелец', 'ОГРН', 'ИНН', 'КПП', 'ОКПО', 'СВЯЗИ ПО РУКОВОДИТЕЛЮ', 'СВЯЗИ ПО УЧРЕДИТЕЛЮ', 'СВЯЗИ ПО УПРАВЛЕНИЮ', 'СВЯЗИ ПО УЧРЕЖДЕНИЮ', 'СВЯЗИ']] = first10160org[0][1000:2000].apply(lambda x: pd.Series(all_parse(x)))

import networkx as nx
G=nx.DiGraph()

def s(s):
    print(s['ИНН'])
    
    G.add_node(s['ИНН'], ogrn=s['ОГРН'], kpp=s['КПП'], okpo=s['ОКПО'])
    
    for i in s['СВЯЗИ ПО РУКОВОДИТЕЛЮ']:
        k=list(i.keys())[0]
        v=list(i.values())[0]
        G.add_edge(k, s['ИНН'])
        G.add_edge(k, v)
        
        

    for i in s['СВЯЗИ ПО УЧРЕДИТЕЛЮ']:
        k=list(i.keys())[0]
        v=list(i.values())[0]
        G.add_edge(k, s['ИНН'])
        G.add_edge(k, v) 
        
    for i in s['СВЯЗИ ПО УЧРЕЖДЕНИЮ']:
        G.add_edge(s['ИНН'], i)    
        
        
    for i in s['СВЯЗИ ПО УПРАВЛЕНИЮ']:
        G.add_edge(s['ИНН'], i) 
        
        
    for i in s['СВЯЗИ']:
        G.add_edge(s['ИНН'], i)         
        
        
    return G


def prep2(s):
    p=[]
    for i in s.split("'")[1::2]:
        p.append(int(i))
    return p
    

def prep(s):
    l=[]
    keys=s.split("'")[1::2][0::2]
    val=s.split("'")[1::2][1::2]
    for k, v in zip(keys, val):
        l.append({int(k):int(v)})
    return l

d1=pd.read_excel('Первые 1000 организаций со связями и информацией!.xlsx', dtype={'СВЯЗИ ПО РУКОВОДИТЕЛЮ': list})
d1

d2=pd.read_excel('Первые 1000-2000 организаций со связями и информацией!.xlsx', dtype={'СВЯЗИ ПО РУКОВОДИТЕЛЮ': list})
# d3=pd.read_excel('Первые 2500-3000 организаций со связями и информацией.xlsx', dtype={'СВЯЗИ ПО РУКОВОДИТЕЛЮ': list})
# d4=pd.read_excel('Первые 3000-4000 организаций со связями и информацией.xlsx', dtype={'СВЯЗИ ПО РУКОВОДИТЕЛЮ': list})
# d5=pd.read_excel('Первые 4000-5000 организаций со связями и информацией.xlsx', dtype={'СВЯЗИ ПО РУКОВОДИТЕЛЮ': list})
# d6=pd.read_excel('Первые 5000-6000 организаций со связями и информацией.xlsx', dtype={'СВЯЗИ ПО РУКОВОДИТЕЛЮ': list})
# d7=pd.read_excel('Первые 6000-7000 организаций со связями и информацией.xlsx', dtype={'СВЯЗИ ПО РУКОВОДИТЕЛЮ': list})


d1['СВЯЗИ ПО РУКОВОДИТЕЛЮ']=d1['СВЯЗИ ПО РУКОВОДИТЕЛЮ'].map(prep)

d1['СВЯЗИ ПО УЧРЕДИТЕЛЮ']=d1['СВЯЗИ ПО УЧРЕДИТЕЛЮ'].map(prep)

d1['СВЯЗИ ПО УПРАВЛЕНИЮ']=d1['СВЯЗИ ПО УПРАВЛЕНИЮ'].map(prep2)

d1['СВЯЗИ ПО УЧРЕЖДЕНИЮ']=d1['СВЯЗИ ПО УЧРЕЖДЕНИЮ'].map(prep2)

d1['СВЯЗИ']=d1['СВЯЗИ'].map(prep2)




d2['СВЯЗИ ПО РУКОВОДИТЕЛЮ']=d2['СВЯЗИ ПО РУКОВОДИТЕЛЮ'].map(prep)

d2['СВЯЗИ ПО УЧРЕДИТЕЛЮ']=d2['СВЯЗИ ПО УЧРЕДИТЕЛЮ'].map(prep)

d2['СВЯЗИ ПО УПРАВЛЕНИЮ']=d2['СВЯЗИ ПО УПРАВЛЕНИЮ'].map(prep2)

d2['СВЯЗИ ПО УЧРЕЖДЕНИЮ']=d2['СВЯЗИ ПО УЧРЕЖДЕНИЮ'].map(prep2)

d2['СВЯЗИ']=d2['СВЯЗИ'].map(prep2)


import networkx as nx
G=nx.DiGraph()



d1.apply(lambda x: s(x), axis=1)
d2.apply(lambda x: s(x), axis=1)


import numpy as np
degrees_in = [val for (node, val) in G.in_degree()]
degrees_out = [val for (node, val) in G.out_degree]
degrees = [val for (node, val) in G.degree()]
np.mean(degrees), np.mean(degrees_in), np.mean(degrees_out)




nx.write_edgelist(G, "graph_edges_2000.csv")

# nx.get_node_attributes(G, 'kpp')

len(G.nodes)

len(G.edges)

node_degree = dict(G.in_degree())
max_degree_node = max(node_degree, key=node_degree.get)
max_degree = node_degree[max_degree_node]

print(f"Узел с максимальной входящей степенью: {max_degree_node}, степень: {max_degree}")

node_degree = dict(G.out_degree())
max_degree_node = max(node_degree, key=node_degree.get)
max_degree = node_degree[max_degree_node]

print(f"Узел с максимальной исходящей степенью: {max_degree_node}, степень: {max_degree}")


