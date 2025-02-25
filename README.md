import csv
from tabulate import tabulate 
from datetime import datetime

# File to store user data
USER_FILE = "users.csv"
# File to store expense data
EXPENSE_FILE = "expenses.csv"

# Function to load users from the CSV file
def load_users():
    try:
        with open(USER_FILE, mode="r") as file:
            reader = csv.reader(file)
            return {rows[0]: rows[1] for rows in reader}
    except FileNotFoundError:
        return {}

# Function to save users to the CSV file
def save_users(users):
    with open(USER_FILE, mode="w", newline="") as file:
        writer = csv.writer(file)
        for username, password in users.items():
            writer.writerow([username, password])

# Function to load expenses from the CSV file
def load_expenses():
    try:
        with open(EXPENSE_FILE, mode="r") as file:
            reader = csv.reader(file)
            return list(reader)
    except FileNotFoundError:
        return []

# Function to save expenses to the CSV file
def save_expenses(expenses):
    with open(EXPENSE_FILE, mode="w", newline="") as file:
        writer = csv.writer(file)
        writer.writerows(expenses)

# Function to register a new user
def register(users):
    username = input("Enter a username: ")
    if username in users:
        print("Username already exists! Please choose another.")
        return
    password = input("Enter a password: ")
    users[username] = password
    save_users(users)
    print("User registered successfully!")

# Function to log in
def login(users):
    username = input("Enter your username: ")
    password = input("Enter your password: ")
    if username in users and users[username] == password:
        print("Login successful!")
        return username
    else:
        print("Invalid username or password!")
        return None

# Function to add an expense
def add_expense(username):
    category = input("Enter expense category (e.g., Food, Transport): ")
    amount = input("Enter expense amount: ")
    date = input("Enter date (YYYY-MM-DD) or leave blank for today: ")
    if not date:
        date = datetime.today().strftime("%Y-%m-%d")
    expenses = load_expenses()
    expenses.append([username, category, amount, date])
    save_expenses(expenses)
    print("Expense added successfully!")

# Function to view all expenses
def view_expenses(username):
    expenses = load_expenses()
    user_expenses = [expense for expense in expenses if expense[0] == username]
    if not user_expenses:
        print("No expenses found!")
    else:
        print("\nYour Expenses:")
        print(tabulate(user_expenses, headers=["Username", "Category", "Amount", "Date"], tablefmt="pretty"))

# Function to delete an expense
def delete_expense(username):
    expenses = load_expenses()
    user_expenses = [expense for expense in expenses if expense[0] == username]
    if not user_expenses:
        print("No expenses found!")
        return
    print("\nYour Expenses:")
    print(tabulate(user_expenses, headers=["Index", "Category", "Amount", "Date"], showindex=True, tablefmt="pretty"))
    try:
        index = int(input("Enter the index of the expense to delete: "))
        if 0 <= index < len(user_expenses):
            expense_to_delete = user_expenses[index]
            expenses.remove(expense_to_delete)
            save_expenses(expenses)
            print(f"Expense deleted: {expense_to_delete}")
        else:
            print("Invalid index!")
    except ValueError:
        print("Please enter a valid number!")

# Main menu
def main():
    users = load_users()
    while True:
        print("\n--- Personal Expense Tracker ---")
        print("1. Register")
        print("2. Login")
        print("3. Exit")
        choice = input("Enter your choice: ")

        if choice == "1":
            register(users)
        elif choice == "2":
            username = login(users)
            if username:
                while True:
                    print("\n--- Expense Management ---")
                    print("1. Add Expense")
                    print("2. View Expenses")
                    print("3. Delete Expense")
                    print("4. Logout")
                    sub_choice = input("Enter your choice: ")

                    if sub_choice == "1":
                        add_expense(username)
                    elif sub_choice == "2":
                        view_expenses(username)
                    elif sub_choice == "3":
                        delete_expense(username)
                    elif sub_choice == "4":
                        break
                    else:
                        print("Invalid choice!")
        elif choice == "3":
            print("Exiting...")
            break
        else:
            print("Invalid choice!")

# Run the program
if __name__ == "__main__":
    main()
