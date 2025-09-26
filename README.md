# personal-finance-tracker
A Python/Pandas Personal Finance Tracker that imports bank statements (CSV/Excel), normalizes &amp; tags transactions, and generates monthly rollups, variance, budgets, cash-flow summaries, and savings goals. Includes what-if budgeting, Matplotlib charts (spend by category, trends), and exportable reports (CSV/PNG)—all local in Jupyter.

# Solution - enter your code solution below

import csv

class Expense:
    def __init__(self, name, category, amount) -> None:
        self.name = name  # Fixes mistake where name was set as category
        self.category = category
        self.amount = amount

    def __repr__(self):
        return f"<Expense: {self.name}, {self.category}, ${self.amount:.2f}>"

def main():
    print(f"Running Expense Tracker!")
    expense_file_path = "expenses.csv"
    
    # Get user input for expenses
    expense = get_user_expense()
    print(expense)
    
    # Write the expense to a file
    save_expense_to_file(expense, expense_file_path)
    
    # Read file and summarize expenses
    summarize_expenses(expense_file_path)

def get_user_expense():
    print(f"Getting User Expense")
    expense_name = input("Enter expense name: ")
    expense_amount = float(input("Enter expense amount: "))

    expense_categories = ["Food", "Home", "Work", "Fun", "Misc"]

    while True:
        print("Select a category: ")
        for i, category_name in enumerate(expense_categories):
            print(f"{i + 1}. {category_name}")

        value_range = f"[1 - {len(expense_categories)}]"
        selected_index = int(input(f"Enter a category number {value_range}: ")) - 1

        if selected_index in range(len(expense_categories)):
            selected_category = expense_categories[selected_index]
            new_expense = Expense(
                name=expense_name, category=selected_category, amount=expense_amount
            )
            return new_expense
        else:
            print("Invalid category. Please try again!")

def save_expense_to_file(expense, file_path):
    """Writes the expense to a CSV file."""
    with open(file_path, mode='a', newline='') as file:
        writer = csv.writer(file)
        writer.writerow([expense.name, expense.category, expense.amount])
    print(f"Expense saved to {file_path}")

def summarize_expenses(file_path):
    """Reads and summarizes the expenses from the CSV file."""
    print(f"Summarizing expenses...")
    try:
        with open(file_path, mode='r') as file:
            reader = csv.reader(file)
            expenses = list(reader)
            
            if not expenses:
                print("No expenses found.")
                return
            
            # Summarize total and by category
            total_expenses = 0
            category_totals = {}

            for expense in expenses:
                name, category, amount = expense
                amount = float(amount)
                total_expenses += amount

                if category in category_totals:
                    category_totals[category] += amount
                else:
                    category_totals[category] = amount

            print(f"Total Expenses: ${total_expenses:.2f}")
            for category, total in category_totals.items():
                print(f"Total for {category}: ${total:.2f}")
    
    except FileNotFoundError:
        print(f"No expenses found in {file_path}. Please add some expenses first.")

if __name__ == "__main__":
    main()
