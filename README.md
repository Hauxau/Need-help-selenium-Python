# Need-help-selenium-Python

from selenium import webdriver
from selenium.common.exceptions import ElementNotVisibleException
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
import pandas as pd
import os
import sys
import csv
import json
from datetime import datetime

from selenium.webdriver.support import expected_conditions as EC
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.chrome.service import Service

options = webdriver.ChromeOptions()
options.add_experimental_option('excludeSwitches', ['enable-logging'])
options.add_experimental_option("detach", True)
#options.add_argument("--headless")

service = Service(ChromeDriverManager().install())
driver = webdriver.Chrome(options=options, service=service)

url = "https://quotes.toscrape.com/"
driver.get(url)
driver.maximize_window()

wait = WebDriverWait(driver, 5).until(EC.url_to_be(url))

containers = driver.find_elements(By.XPATH, "//div[@class='quote']")

citation_liste = []
author_liste = []
author_about_liste = []

for container in containers:
    citation = container.find_element(By.XPATH, "//span[@class='text']").text
    citation_liste.append(citation)
    
    author = container.find_element(By.XPATH, "//small[@class='author']").text
    author_liste.append(author)
                                                       
    author_about = container.find_element(By.XPATH, "//span[2]//a").get_attribute("href")
    author_about_liste.append(author_about)
    
    tags_find = driver.find_elements(By.XPATH,"//div[@class='tags']")
    tags_list = []
    for tag in tags_find:
        tag_finded = tag.find_element(By.XPATH, "//a[@class='tag']").text
        tags_list.append(tag_finded)

QTS_dico = {
            "citation":citation_liste,
            "author":author_liste,
            "tags":author_about_liste,
            "a propos de l'auteur":tags_list
            }

#Ca convertit mon dico en chaine de caractere et peut donc etre ecrit dans un fichier .csv
json_data = json.dumps(QTS_dico)

path_dl = r"c:\Users\Elève\Downloads\test_qts.csv"

os.chdir(r"c:\Users\Elève\Downloads")

if not os.path.join(path_dl):
    with open("test_qts.csv", "w") as file:
        file.write(json_data)
        #
        if not os.path.exists(r"c:\Users\Elève\Downloads\test_qts.json"):
            with open("test_qts.csv", "w") as file:
                file.write(json_data)
        #
        file.close()
elif os.path.join(path_dl):
    with open("test_qts.csv", "a") as file:
        file.write(json_data)
        #
        if os.path.exists(r"c:\Users\Elève\Downloads\test_qts.json"):
            with open("test_qts.csv", "a") as file:
                file.write(json_data)
        #
        file.close()
else:
    print("ptn y a un bleme")


driver.quit()
