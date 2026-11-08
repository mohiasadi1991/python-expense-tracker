import json
from pathlib import Path

FILE = Path("transactions.jso")

def load_data():
    if FILE.exists():
        return json.loads(FILE.read_text())
    return []

def save_data(data):
    FILE.write_text(json.dumps(data, indent=4))

def add_transaction(data):
    kind = input("Type (income/expense): ").lower()

    if kind not in ("income", "expense"):
        print("Invalid transaction type.")
        return

    try:
        amount = float(input("Amount: "))
        if amount <= 0:
            print("Amount must be positive.")
            return
    except ValueError:
        print("Please enter a valid amount.")
        return

    category = input("Category: ").strip()
    if not category:
        print("Category cannot be empty.")
        return

    data.append({
        "type": kind,
        "amount": amount,
        "category": category
    })

    save_data(data)
    print("Transaction saved!")

def show_transactions(data):
    if not data:
        print("No transactions found.")
        return

    for i, item in enumerate(data, 1):
        print(
            f"{i}. {item['type'].title()} | "
            f"{item['category']} | "
            f"${item['amount']:.2f}"
        )

def show_balance(data):
    income = sum(
        x["amount"] for x in data
        if x["type"] == "income"
    )

    expenses = sum(
        x["amount"] for x in data
        if x["type"] == "expense"
    )

    print(f"Total income: ${income:.2f}")
    print(f"Total expenses: ${expenses:.2f}")
    print(f"Balance: ${income - expenses:.2f}")

def main():
    data = load_data()

    while True:
        print("\n=== Expense Tracker ===")
        print("1. Add transaction")
        print("2. View transactions")
        print("3. Show balance")
        print("4. Exit")

        choice = input("Choose an option: ")

        if choice == "1":
            add_transaction(data)
        elif choice == "2":
            show_transactions(data)
        elif choice == "3":
            show_balance(data)
        elif choice == "4":
            print("Goodbye!")
            break
        else:
            print("Invalid option.")

if __name__ == "__main__":
    main()
