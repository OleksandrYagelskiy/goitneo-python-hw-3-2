from collections import UserDict
import datetime

class Field: 
    def __init__(self, value):
        self.value = value

    def __str__(self):
        return str(self.value)

class Name(Field): 
    def __init__(self, value):
        super().__init__(value)

class Phone(Field): 
    def __init__(self, value):
        if not self._validate_phone(value):
            raise ValueError("Invalid phone number format")
        super().__init__(value)

    def _validate_phone(self, value):
        return len(value) == 10 and value.isdigit()

class Birthday(Field): 
    def __init__(self, value):
        if not self._validate_birthday(value):
            raise ValueError("Invalid birthday format")
        super().__init__(value)

    def _validate_birthday(self, value):
        try:
            datetime.datetime.strptime(value, '%d.%m.%Y')
            return True
        except ValueError:
            return False

class Record: 
    def __init__(self, name):
        self.name = Name(name)
        self.phones = []
        self.birthday = None

    def add_phone(self, phone):
        self.phones.append(Phone(phone))

    def remove_phone(self, phone):
        self.phones = [p for p in self.phones if str(p) != phone]

    def edit_phone(self, old_phone, new_phone):
        for i, phone in enumerate(self.phones):
            if str(phone) == old_phone:
                self.phones[i] = Phone(new_phone)

    def find_phone(self, phone):
        for p in self.phones:
            if str(p) == phone:
                return p

    def add_birthday(self, birthday):
        self.birthday = Birthday(birthday)

    def __str__(self):
        phones_str = "; ".join(str(p) for p in self.phones)
        birthday_str = f", birthday: {self.birthday}" if self.birthday else ""
        return f"Contact name: {self.name}, phones: {phones_str}{birthday_str}"

class AddressBook(UserDict): 
    def add_record(self, record):
        self.data[record.name.value] = record

    def find(self, name):
        return self.data.get(name)

    def delete(self, name):
        del self.data[name]

    def get_birthdays_per_week(self):
        today = datetime.datetime.today().date()
        next_week_start = today + datetime.timedelta(days=(7 - today.weekday()))
        next_week_end = next_week_start + datetime.timedelta(days=7)
        
        birthdays = []

        for record in self.data.values():
            if record.birthday:
                birthday = datetime.datetime.strptime(record.birthday.value, '%d.%m.%Y').date()
                if next_week_start <= birthday < next_week_end:
                    birthdays.append(record)

        return birthdays

def parse_input(user_input):  
    cmd, *args = user_input.split()
    cmd = cmd.strip().lower()
    return cmd, args

def input_error(func):
    def inner(*args, **kwargs):
        try:
            return func(*args, **kwargs)
        except ValueError:
            return "Give me name and phone please."
        except KeyError:
            return "Contact not found."
        except IndexError:
            return "Missing command arguments."

    return inner

@input_error
def add_contact(args, contacts):
    name, phone = args
    record = Record(name)
    record.add_phone(phone)
    contacts.add_record(record)
    return "Contact added."

@input_error
def change_contact(args, contacts):
    name, phone = args
    record = contacts.find(name)
    if record:
        record.add_phone(phone)
        return "Contact updated."
    else:
        raise KeyError

@input_error
def show_phone(args, contacts):
    name = args[0]
    record = contacts.find(name)
    if record:
        return record.phones[0]
    else:
        raise KeyError

def show_all(contacts):
    if contacts:
        return '\n'.join([str(record) for record in contacts.data.values()])
    else:
        return "No contacts found."

@input_error
def add_birthday(args, contacts):
    name, birthday = args
    record = contacts.find(name)
    if record:
        record.add_birthday(birthday)
        return "Birthday added."
    else:
        raise KeyError

@input_error
def show_birthday(args, contacts):
    name = args[0]
    record = contacts.find(name)
    if record and record.birthday:
        return record.birthday
    else:
        return "No birthday found."

def birthdays(contacts):
    upcoming_birthdays = contacts.get_birthdays_per_week()
    if upcoming_birthdays:
        return '\n'.join([str(record) for record in upcoming_birthdays])
    else:
        return "No upcoming birthdays."

def main():
    contacts = AddressBook()
    print("Welcome to the assistant bot!")

    while True:
        try:
            user_input = input("Enter a command: ")
            command, args = parse_input(user_input)

            if command in ["close", "exit"]:
                print("Good bye!")
                break

            elif command == "hello":
                print("How can I help you?")

            elif command == "add":
                print(add_contact(args, contacts))

            elif command == "change":
                print(change_contact(args, contacts))

            elif command == "phone":
                print(show_phone(args, contacts))

            elif command == "all":
                print(show_all(contacts))

            elif command == "add-birthday":
                print(add_birthday(args, contacts))

            elif command == "show-birthday":
                print(show_birthday(args, contacts))

            elif command == "birthdays":
                print(birthdays(contacts))

            else:
                print("Invalid command.")

        except Exception as e:
            print(e)

if __name__ == "__main__":
    main()
