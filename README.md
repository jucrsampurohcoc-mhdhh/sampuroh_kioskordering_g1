import datetime

FOODS = {
    1: ("Burger", 95.00),
    2: ("Chicken (1pc)", 110.00),
    3: ("Spaghetti", 75.00),
}

DRINKS = {
    4: ("Coke", 35.00),
    5: ("Iced Tea", 30.00),
    6: ("Water", 20.00),
}

def show_menu():
    print("-------- MENU --------")
    print("Foods:")
    for code, (name, price) in FOODS.items():
        print(f" {code}. {name:<15} ₱{price:.2f}")

    print("Drinks:")
    for code, (name, price) in DRINKS.items():
        print(f" {code}. {name:<15} ₱{price:.2f}")
    print("----------------------")

def get_item(code):
    if code in FOODS:
        return FOODS[code]
    elif code in DRINKS:
        return DRINKS[code]
    else:
        return None

def show_order(order):
    if not order:
        print("\nYour order list is empty.")
        return
    print("\n--- CURRENT ORDER ---")
    for i, item in enumerate(order, 1):
        print(f"{i}. {item['qty']} x {item['name']} = ₱{item['price'] * item['qty']:.2f}")
    print("---------------------")

def delete_item(order):
    show_order(order)
    if not order:
        return
    try:
        num = int(input("Enter the number of the item to delete: "))
        if 1 <= num <= len(order):
            removed = order.pop(num - 1)
            print(f"{removed['name']} has been removed from your order.")
        else:
            print("Invalid number.")
    except ValueError:
        print("Please enter a valid number.")

def take_order():
    order = []
    print("Hello! Welcome to our Food Ordering Kiosk!\n")

    while True:
        show_menu()
        print("\nType 'done' to finish ordering.")
        print("Type 'delete' to remove an item from your order.\n")
        choice = input("Enter item number: ").strip().lower()

        if choice == "done":
            if order:
                break
            else:
                print("You haven't ordered anything yet.")
                continue

        if choice == "delete":
            delete_item(order)
            continue

        if not choice.isdigit():
            print("Please enter a valid number.")
            continue

        code = int(choice)
        item = get_item(code)
        if not item:
            print("Invalid item number.")
            continue

        qty_input = input("Enter quantity: ").strip()
        if not qty_input.isdigit() or int(qty_input) <= 0:
            print("Please enter a valid quantity.")
            continue

        qty = int(qty_input)
        name, price = item
        order.append({"name": name, "price": price, "qty": qty})
        print(f"Added {qty} x {name}.\n")

    while True:
        mode = input("Dine-in or Take-out? (d/t): ").strip().lower()
        if mode in ("d", "t"):
            mode_str = "DINE-IN" if mode == "d" else "TAKE-OUT"
            break
        print("Please type 'd' or 't'.")
    return order, mode_str

def compute_total(order):
    total = 0
    for item in order:
        total += item["price"] * item["qty"]
    return round(total, 2)

def payment_flow(total):
    print(f"\nYour total is ₱{total:.2f}")
    while True:
        cash_input = input("Enter cash amount: ").strip().replace(",", "")
        try:
            cash = float(cash_input)
        except ValueError:
            print("Please enter a number.")
            continue
        if cash < total:
            print(f"Not enough money. You still need ₱{(total - cash):.2f}")
            continue
        change = round(cash - total, 2)
        return cash, change

def print_receipt(order, mode, total, paid, change):
    date_today = datetime.date.today().strftime("%Y-%m-%d")
    print("\n" + "-" * 35)
    print("       SIMPLE KIOSK RECEIPT")
    print(f"            {date_today}")
    print(f"             {mode}")
    print("-" * 35)
    for item in order:
        print(f"    {item['qty']} x {item['name']:<15} ₱{item['price'] * item['qty']:.2f}")
    print("-" * 35)
    print(f"      TOTAL:          ₱{total:.2f}")
    print(f"      CASH:           ₱{paid:.2f}")
    print(f"      CHANGE:         ₱{change:.2f}")
    print("-" * 35)
    print("  Thank you for ordering with us!")
    print("-" * 35)

def main():
    order, mode = take_order()
    total = compute_total(order)
    paid, change = payment_flow(total)
    print_receipt(order, mode, total, paid, change)

if __name__ == "__main__":
    main()
