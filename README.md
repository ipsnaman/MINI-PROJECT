# MINI-PROJECT
import csv
import os
from datetime import datetime
from collections import defaultdict

# File to store the expenses
expense_file = 'expenses.csv'

# Function to load expenses from the CSV file
def load_expenses():
    expenses = []
    if os.path.exists(expense_file):
        with open(expense_file, 'r') as file:
            reader = csv.DictReader(file)
            for row in reader:
                row['Amount'] = float(row['Amount'])
                expenses.append(row)
    return expenses

# Function to save expenses to the CSV file
def save_expenses(expenses):
    with open(expense_file, 'w', newline='') as file:
        fieldnames = ['Amount', 'Category', 'Date']
        writer = csv.DictWriter(file, fieldnames=fieldnames)
        writer.writeheader()
        writer.writerows(expenses)

# Function to add a new expense
def add_expense(expenses):
    amount = float(input("Enter the amount: $"))
    category = input("Enter the category (e.g., Food, Transport, Entertainment): ")
    date = input("Enter the date (YYYY-MM-DD) or press Enter for today's date: ")

    if not date:
        date = datetime.now().strftime('%Y-%m-%d')

    expenses.append({'Amount': amount, 'Category': category, 'Date': date})
    save_expenses(expenses)
    print("Expense added successfully!")

# Function to view total spending by category
def view_spending_by_category(expenses):
    category = input("Enter the category you want to see spending for: ")
    total = sum(exp['Amount'] for exp in expenses if exp['Category'].lower() == category.lower())
    print(f"Total spending for {category}: ${total:.2f}")

# Function to view total spending overall
def view_total_spending(expenses):
    total = sum(exp['Amount'] for exp in expenses)
    print(f"Total overall spending: ${total:.2f}")

# Function to view spending over time (daily, weekly, or monthly summaries)
def view_spending_over_time(expenses):
    summary_type = input("View spending by (daily/weekly/monthly): ").lower()
    
    time_summary = defaultdict(float)
    
    for exp in expenses:
        date = datetime.strptime(exp['Date'], '%Y-%m-%d')
        if summary_type == 'daily':
            key = date.strftime('%Y-%m-%d')
        elif summary_type == 'weekly':
            key = date.strftime('%Y-%U')  # Year-weeknumber
        elif summary_type == 'monthly':
            key = date.strftime('%Y-%m')  # Year-month
        else:
            print("Invalid option.")
            return
        
        time_summary[key] += exp['Amount']
    
    for period, total in time_summary.items():
        print(f"{summary_type.capitalize()} {period}: ${total:.2f}")

# Main menu function
def menu():
    expenses = load_expenses()
    
    while True:
        print("\n--- Personal Expense Tracker ---")
        print("1. Add an expense")
        print("2. View total spending by category")
        print("3. View total overall spending")
        print("4. View spending over time (daily/weekly/monthly)")
        print("5. Exit")
        
        choice = input("Choose an option: ")

        if choice == '1':
            add_expense(expenses)
        elif choice == '2':
            view_spending_by_category(expenses)
        elif choice == '3':
            view_total_spending(expenses)
        elif choice == '4':
            view_spending_over_time(expenses)
        elif choice == '5':
            print("Exiting the program. Goodbye!")
            break
        else:
            print("Invalid choice. Please try again.")

# Run the expense tracker
if __name__ == "__main__":
    menu()
