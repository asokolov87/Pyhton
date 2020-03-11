import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.base import MIMEBase
from email.header import Header
from email import encoders
from datetime import datetime
import os
import configparser

config = configparser.ConfigParser()
config.read("settings.ini")

company = config["main"]["company"]
host = config["main"]["host"]
login = config["main"]["login"]
password = config["main"]["password"]
dir_doc = config["main"]["dir_doc"]

#--------------------------------------------
#отправка почты
#если новых файлов нет, пропускаем
def send_mail(files, mail):

    if len(files) == 0:
        return
        
    HOST = host
    SUBJECT = "Тема"
    TO = mail
    FROM = "mail@mail.ru"

    msg = MIMEMultipart()
    msg['Subject'] = Header(SUBJECT, 'utf-8')
    msg['From'] = FROM
    msg['To'] = TO

    for element in files:
        path = MIMEBase('application', "octet-stream")
        path.set_payload(open(element, "rb").read())
        encoders.encode_base64(path)
        path.add_header('Content-Disposition', 'attachment', filename=os.path.basename(element))
        msg.attach(path)

    server = smtplib.SMTP(HOST)
    try:
        server.starttls()
        server.login(login, password)
        server.sendmail(FROM, TO, msg.as_string())
    finally:
        server.quit()

#--------------------------------------------
# поиск неотправленных файлов
# возвращается List названий файлов
# все отправленные файлы записываются в логер
def find_no_send_file(company_name):

    d = datetime.now()
    data_year = d.strftime("%Y")
    data_mounth = d.strftime("%m")

    # папка
    dir = dir_doc

    # определяем итоговую папку на месяц
    dir_company = data_year + "\\" + company_name + "\\"
    dir_full = dir + dir_company

    list_dir = os.listdir(path=dir_full)
    dir_mounth = ""

    for line in list_dir:
        if line[0:2] == data_mounth:
            dir_mounth = line
            break

    dir_final = dir_full + dir_mounth  # итоговая папка

    # создаем логер если нет
    file_name = data_mounth + "." + data_year + "." + company_name
    f = open(file_name, 'a')
    f.close()

    # читаем данные с логера
    logger = open(file_name, 'r')
    list_log = []
    for line in logger:
        list_log.append(line[:-1])
    logger.close()

    # Чтение списка файлов в папке
    list_files = os.listdir(path=dir_final)

    # находим неотправленные файлы
    result = list(set(list_files) ^ set(list_log))
    result.sort()
    no_send_fullname_file = []
    for line in result:
        no_send_fullname_file.append(dir_final + "\\" + line)

    # дозаписываем логгер
    f = open(file_name, 'a+')
    for index in result:
        f.write(index + '\n')
    f.close()

    return no_send_fullname_file

for element in company:
    files = find_no_send_file(element)
    if element == "":
        send_mail(files, "")
    if element == "":
        send_mail(files, "")
    if element == "":
        send_mail(files, "")
