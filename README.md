# banking-system

from prettytable import PrettyTable
import random
import mysql.connector
import random
#import socket
#socket.getaddrinfo('localhost', 3306)
# to generate a random number
def randN(N):
	min = pow(10, N-1)
	max = pow(10, N) - 1
	return random.randint(min, max)


#coding for connection establishment
con1 = mysql.connector.connect(host='localhost', user='root', password='1234')

#if conic.isconnected():
#    print("connection established")
#else:
#    print("connection failed")
mycursor=con1.cursor()
mycursor.execute("create database if not exists bank_sys1")
mycursor.execute("use bank_sys1")
mycursor.execute("create table if not exists signup(username varchar(40),password varchar(40))")
#here selecting all the contents from the table name entered in code

#coding for projrct
def signup():
    username = input("USERNAME:")
    password = input("PASSWORD:")
    mycursor.execute("insert into signup values("+username+","+password+")")
    con1.commit()
    print("\t\t\t***************>>>SIGNED UP SUCCESSFULLY<<<***************")
    print("Now Please Login To Continue....")
    print("****note: Please do not share your username and passwword with anyone****")
    login()

def login():
    username = input("USERNAME:")
    password = input("PASSWORD:")

    mycursor=con1.cursor()
    mycursor.execute("select username from signup") #going to use a sql query to check the data entered is accurate...

    default_user = mycursor.fetchall()
    users = []  #all the usernames will be strored in this users and they will be added by default_user
    for i in range(len(default_user)):
        users.append(default_user[i][0])

    mycursor = con1.cursor()
    mycursor.execute("select password from signup")
    default_paswd = mycursor.fetchall()
    paswds = []    #all the passwords will be strored in this paswds and they will be added by default_paswd
    for i in range(len(default_paswd)):
        paswds.append(default_paswd[i][0])
    con1.commit()

    if(username not in users ) or (password not in paswds):
      print("INVALID USERNAME OR PASSWORD!")
      attempt = 1
      while True:
          attempt = int(input("PRESS: \n1: TO TRY AGAIN.... \nANY NUMBER: TO EXIT...."))
          if attempt==1:
              login()
          else:
              exit()

    else:
        mycursor = con1.cursor()
        mycursor.execute("select username from signup where username="+username+"") #will perform a sql task by selecting the password from database
        user=mycursor.fetchone()
        mycursor.execute("select password from signup where passowrd="+password+"")
        passwrd= mycursor.fetchone()
        print("\t\t\t**************>>>LOGGEDIN SUCCESSFULLY!<<<**************")
        con1.commit()
        #asking user to enter which task user wishes to perform
        while True:
            print("Press:")
            print("1: To Open A New Account!")
            print("2: To Deposite amount!")
            print("3: To Withdraw amount!")
            print("4: Bank Enquiry!")
            print("5: Customer Details!")
            print("6: To Update Personal Information!")
            print("7: To Close account!")
            print("8: To Show Infromation/Data of user!")
            print("TO EXIT: PRESS ANY!....")
            choice = int(input("PLEASE ENTER YOUR CHOICE!...."))
            #to open a new account
            if choice==1:
                open_account()
            #for new deposite
            elif choice ==2:
                deposite_amount()
            # to withdraw amount
            elif choice == 3:
                withdraw_amount()
            # to check for balance
            elif choice == 4:
                check_balance()
            #to enquire customer details
            elif choice == 5:
                customer_details()
            #to update personal information
            elif choice == 6:
                update_info()
            #to close account
            elif choice == 7:
                close()
            #to show all customer info
            elif choice == 8:
                show_info()
            # to exit
            else:
                print("\t\t\t\t LOGGED OUT SUCCESSFULLY!!....")
                print("note: To proceed any action further in account login required!....")
                break

# to open new account
def open_account():
    name= input("ENTER FULL NAME...")
    account_number = print(randN(4))
    address = input("ENTER YOUR CURRENT PARMANENT ADDRESS!....")
    contact_no= int(input("ENTER YOUR MOBILE NUMBER!"))
    total_balance = int(input("DEPOSITE SOME AMOUNT TO OPEN A ACCOUNT!!"))
    account_pin = int(input("ENTER A 4 DIGIT PIN!"))

    #data1 is for a table named as account
    data1 = (name,account_number,address,contact_no,total_balance,account_pin)
    # data2 is for amount table
    data2 = (name,account_number,total_balance,account_pin)

    #entering queries in sql
    mycursor.execute("create table if not exists account(name varchar(40),account_number int(40),address varchar(50),contact_no int(40),total_balance int,account_pin int(20))")
    mycursor.execute("create table if not exists amount(name varchar(40),account_number int(40),total_balance int,account_pin int(20))")

    account_table = "insert into account values(%s,%s,%s,%s,%s,%s)"
    amount_table = "insert into amount values(%s,%s,%s,%s)"

    c = con1.cursor()
    #to execute the above code and insert data into mysql
    mycursor.execute(account_table,data1)
    mycursor.execute(amount_table,data2)
    con1.commit()

    print(" ")
    print("\t\t\t _________********>>>>BANK ACCOUNT CREATED SUCESSFULLY!!<<<<********_________")
    print(" ")
    print("YOUR NEW BANK ACCOUNT NUMBER IS:",account_number,"\nNOTE:","\n Keep your account number safely and do not share with anyone...")
    print("___________________________________________________________________________________________________________")

def deposite_amount():
    name = input("ENTER FULL NAME...")
    account_number = input("ENTER YOUR ACCOUNT NUMBER")
    pin= int(input("enter your 4 digit pin"))
    deposite_amnt = int(input(("ENTER THE AMOUNT U WANT TO DEPOSITE")))

    c = con1.cursor()
    mycursor.execute("update account_table set total_balance+"+deposite_amnt+"where acoount_number+"+account_number+";")
    con1.commit()
    mycursor.execute("select total_balance from amount_table where account_number="+str(account_number))
    myresult = mycursor.fetchall()
    pt = PrettyTable(["total_balance"])
    for total_balance in myresult:
        pt.add_row([total_balance])
    print("\t\t\t _________********>>>>TOTAL BALANCE<<<<********_________")
    print(pt)
    print("___________________________________________________________________________________________________________")

def withdraw_amount():
    name = input("ENTER FULL NAME...")
    account_number = input("ENTER YOUR ACCOUNT NUMBER")
    pin = int(input("enter your 4 digit pin"))
    deposite_amnt = int(input(("ENTER THE AMOUNT U WANT TO DEPOSITE")))

    c = con1.cursor()
    mycursor.execute("update account_table set total_balance-" + deposite_amnt + "where acoount_number+" + account_number + ";")
    con1.commit()
    mycursor.execute("select total_balance from amount_table where account_number=" + str(account_number))
    myresult = mycursor.fetchall()
    pt = PrettyTable(["total_balance"])
    for total_balance in myresult:
        pt.add_row([total_balance])
    print("\t\t\t _________********>>>>TOTAL BALANCE after WITHDRAW<<<<********_________")
    print(pt)
    print("___________________________________________________________________________________________________________")

def check_balance():
    account_number = input("ENTER YOUR ACCOUNT NUMBER")
    pin = int(input("enter your 4 digit pin"))

    c = con1.cursor()
    mycursor.execute("select total_balance from amount_table where account_number=" + str(account_number))
    myresult = mycursor.fetchall()
    pt = PrettyTable(["total_balance"])
    for total_balance in myresult:
        pt.add_row([total_balance])
    print("\t\t\t _________********>>>>TOTAL BALANCE ENQUIRY SUCESSFULLY PRINTED!!<<<<********_________")
    print(pt)
    print("___________________________________________________________________________________________________________")

def customer_details():
    account_number = input("ENTER YOUR ACCOUNT NUMBER")
    pin = int(input("enter your 4 digit pin"))

    c = con1.cursor()
    mycursor.execute("select * from amount_table where account_number=" + str(account_number))
    myresult = mycursor.fetchall()

    pt = PrettyTable(["name","account_number","address","contact_no","total_balance"])
    for name,account_number,address,contact_no,total_balance in myresult:
        pt.add_row([name,account_number,address,contact_no,total_balance])
    print("\t\t\t _________********>>>>COSTOMER DETAILS!!<<<<********_________")
    print(pt)
    print("___________________________________________________________________________________________________________")

def update_info():
    account_number = input("ENTER YOUR ACCOUNT NUMBER")
    pin = int(input("enter your 4 digit pin"))
    new_contact = input("ENTER NEW CONTACT NUMBER!!")
    c = con1.cursor()
    mycursor.execute("update account_table set contact_no="+new_contact+"where account_number="+account_number+";")
    conq.commit()
    mycursor.execute("select * from amount_table where account_number=" + str(account_number))
    myresult = mycursor.fetchall()

    pt = PrettyTable(["name", "account_number", "address", "contact_no", "total_balance"])
    for name, account_number, address, contact_no, total_balance in myresult:
        pt.add_row([name, account_number, address, contact_no, total_balance])
    print("\t\t\t _________********>>>>INFORMATION UPDATED SUCCESSFULLY!!<<<<********_________")
    print(pt)
    print("___________________________________________________________________________________________________________")

def close():
    name = input("ENTER FULL NAME...")
    account_number = input("ENTER YOUR ACCOUNT NUMBER")
    pin = int(input("enter your 4 digit pin"))

    c = con1.cursor()
    mycursor.execute("delete from amount_table where account_number=" + str(account_number))
    con1.commit()
    print("\t\t\t _________********>>>>ACCOUNT DELETED/CLOSED SUCCESSFULLY!!<<<<********_________")
    print('__________________________________________________________________________________________________________')


def show_info():
    mycursor = con1.cursor()
    mycursor.execute("select * from account_table")
    myresult = mycursor.fetchall()

    pt = PrettyTable(["name", "account_number", "address", "contact_no", "total_balance"])
    for name, account_number, address, contact_no, total_balance in myresult:
        pt.add_row([name, account_number, address, contact_no, total_balance])
    print("\t\t\t _________********>>>>ALL INFORMATION ABOUT THIS ACCOUNT!!<<<<********_________")
    print(pt)
    print("___________________________________________________________________________________________________________")


print("___________________________________________________________________________________________________________")
print("___________________________________________________________________________________________________________")
print("______________________________________RK BANK ONLINE PORTAL________________________________________________")
print("_____________________________________________RK BANK_______________________________________________________")
print("___________________________________________________________________________________________________________")
print("________________________________MAKING BANKING EASY FOR EVERYONE___________________________________________")


print("PRESS:")
print("\t1:SIGNUP\n\n \t2:LOGIN")
ch = int(input("\n\n\t SIGNUP / LOGIN(1,2):"))
if ch==1:
    signup()
if ch==2:
    login()
else:
    print("WRONG ENTRY TRY AGAIN....")
