import csv
import os

DATA_FILE = "students_clean.csv"
RAW_FILE = "contacts.csv"

# Complete 20-row sample dataset provided in the assignment
RAW_CSV_DATA = """name,phone,email
Aarav Sharma,9876543210,aarav.sharma@gmail.com
Priya Patel,8123456789,priya_p@outlook.com
Rohan Verma,7001122334,rohan.v@yahoo.co.in
Ananya Sen,5551234567,ananya.sen@gmail.com
Rahul Das,,rahul.das@college.edu
Sneha Roy,9876543210,sneha.roy@gmail.com
,9123456780,noname@gmail.com
Vikram Singh,9898989898,vikram.singh@@gmail.com
Kavita Nair,6234567890,kavita.nair@gmail.com
Arjun Kapoor,9876501234,arjun@kapoor
Neha Gupta,8765432109,neha.gupta@outlook.com
Aditya Joshi,7890123456,aditya joshi@gmail.com
Pooja Mehta,9988776655,pooja.mehta@yahoo.com
Deepak Kumar,6345678901,deepak.kumar@domain.com
Siddharth Rao,9112233445,siddharth.rao@
Meera Iyer,8223344556,meera.iyer@gmail.com
Karan Malhotra,9334455667,karan.malhotra@rediffmail.com
Ritu Choudhary,7445566778,ritu.c@gmail.com
Vikas Khanna,9556677889,vikas.khanna@outlook.com
Simran Kaur,8667788990,simran.kaur@yahoo.com
"""

def initialize_raw_file():
    if not os.path.exists(RAW_FILE):
        with open(RAW_FILE, mode='w', encoding='utf-8') as f:
            f.write(RAW_CSV_DATA.strip())


def validate_contact(name, phone, email):
    name = name.strip()
    phone = phone.strip()
    email = email.strip()
    
    # Check 1: Validate Name
    if len(name) == 0:
        return (False, "Name is blank")
        
    # Check 2: Validate Phone (10 digits, only digits, starts with 6, 7, 8, or 9)
    if not (len(phone) == 10 and phone.isdigit() and phone[0] in ['6', '7', '8', '9']):
        return (False, "Invalid 10-digit phone")
        
    # Check 3: Validate Email (Exactly one @, no spaces, valid domain and period placement)
    if email.count('@') != 1 or ' ' in email:
        return (False, "Malformed email")
        
    local_part, domain_part = email.split('@')
    if len(local_part) == 0 or len(domain_part) == 0:
        return (False, "Malformed email")
        
    if '.' not in domain_part or domain_part.startswith('.') or domain_part.endswith('.'):
        return (False, "Malformed email")
        
    return (True, "Valid")


def load_and_clean_data(input_filename=RAW_FILE, output_filename=DATA_FILE):
    initialize_raw_file()
    total_rows = 0
    valid_rows = 0
    clean_records = []
    
    seen_phones = set()
    seen_emails = set()

    print("--- Ingestion & Validation Log ---")
    with open(input_filename, mode='r', encoding='utf-8') as file:
        reader = csv.DictReader(file)
        for row in reader:
            total_rows += 1
            name = row.get('name', '')
            phone = row.get('phone', '')
            email = row.get('email', '')
            
            is_valid, reason = validate_contact(name, phone, email)
            
            if not is_valid:
                print(f"Rejected ({name.strip()},{phone.strip()},{email.strip()}): {reason}")
                continue
                
            # Check for duplicates
            if phone in seen_phones:
                print(f"Duplicate (Phone {phone}): {name.strip()} already registered.")
                continue
            if email in seen_emails:
                print(f"Duplicate (Email {email}): {name.strip()} already registered.")
                continue
                
            seen_phones.add(phone)
            seen_emails.add(email)
            clean_records.append({"name": name.strip(), "phone": phone.strip(), "email": email.strip()})
            valid_rows += 1

    # Write clean records to output file
    with open(output_filename, mode='w', newline='', encoding='utf-8') as outfile:
        fieldnames = ["name", "phone", "email"]
        writer = csv.DictWriter(outfile, fieldnames=fieldnames)
        writer.writeheader()
        writer.writerows(clean_records)
        
    print("-" * 40)
    print(f"Total Rows Read       : {total_rows}")
    print(f"Valid Rows Accepted   : {valid_rows}")
    print(f"Rejected / Duplicates : {total_rows - valid_rows}")
    print(f"Clean records saved to {output_filename}\n")
    return clean_records


# ==========================================
# 3. BUSINESS INTELLIGENCE MODULE (analytics.py logic)
# ==========================================
def generate_analytics(raw_filename=RAW_FILE, clean_records=None):
    if clean_records is None:
        clean_records = load_students_from_csv()
        
    total_processed = 0
    if os.path.exists(raw_filename):
        with open(raw_filename, mode='r', encoding='utf-8') as file:
            reader = csv.DictReader(file)
            for _ in reader:
                total_processed += 1
    else:
        total_processed = len(clean_records)
            
    valid_count = len(clean_records)
    rejected_count = max(0, total_processed - valid_count)
    accuracy = (valid_count / total_processed) * 100 if total_processed > 0 else 0
    
    # Telecom Network Distribution (by starting digit)
    telecom_breakdown = {'9': 0, '8': 0, '7': 0, '6': 0}
    for student in clean_records:
        if student['phone']:
            first_digit = student['phone'][0]
            if first_digit in telecom_breakdown:
                telecom_breakdown[first_digit] += 1
            
    # Email Domain Breakdown
    domain_breakdown = {}
    for student in clean_records:
        if '@' in student['email']:
            domain = student['email'].split('@')[1]
            domain_breakdown[domain] = domain_breakdown.get(domain, 0) + 1
        
    sorted_domains = sorted(domain_breakdown.items(), key=lambda x: x[1], reverse=True)
    
    print("\n=======================================================")
    print("      STUDENT REGISTRATION - BUSINESS INTELLIGENCE     ")
    print("=======================================================")
    print(f"Total Contacts Processed : {total_processed}")
    print(f"Valid Registrations       : {valid_count}")
    print(f"Rejected Records          : {rejected_count}")
    print(f"Data Quality Accuracy     : {accuracy:.1f}%")
    print("\n--- Telecom Network Breakdown ---")
    print(f"  9-Series (e.g. Jio/Airtel) : {telecom_breakdown['9']} students")
    print(f"  8-Series (e.g. Vi/Airtel)  : {telecom_breakdown['8']} students")
    print(f"  7-Series (e.g. BSNL/Vi)    : {telecom_breakdown['7']} students")
    print(f"  6-Series                   : {telecom_breakdown['6']} student")
    print("\n--- Top Email Domains ---")
    for domain, count in sorted_domains:
        print(f"  @{domain:<18} : {count} student{'s' if count > 1 else ''}")
    print("=======================================================")



def load_students_from_csv():
    students = []
    if not os.path.exists(DATA_FILE):
        return load_and_clean_data(RAW_FILE, DATA_FILE)
    with open(DATA_FILE, mode='r', encoding='utf-8') as f:
        reader = csv.DictReader(f)
        for row in reader:
            students.append(row)
    return students

def save_students_to_csv(students):
    with open(DATA_FILE, mode='w', newline='', encoding='utf-8') as f:
        fieldnames = ["name", "phone", "email"]
        writer = csv.DictWriter(f, fieldnames=fieldnames)
        writer.writeheader()
        writer.writerows(students)

def view_all_students(students):
    print("\n--- Registered Students ---")
    if not students:
        print("No student records found.")
        return
    print(f"{'ID':<5} | {'Name':<20} | {'Phone':<12} | {'Email':<30}")
    print("-" * 75)
    for idx, s in enumerate(students, start=1):
        print(f"{idx:<5} | {s['name']:<20} | {s['phone']:<12} | {s['email']:<30}")

def search_student(students):
    query = input("Enter search query (Name, Phone, or Email): ").strip().lower()
    results = [s for s in students if query in s['name'].lower() or query in s['phone'] or query in s['email'].lower()]
    
    if not results:
        print("No matching student records found.")
        return
        
    print(f"\nFound {len(results)} matching record(s):")
    print(f"{'Name':<20} | {'Phone':<12} | {'Email':<30}")
    print("-" * 65)
    for s in results:
        print(f"{s['name']:<20} | {s['phone']:<12} | {s['email']:<30}")

def register_student(students):
    print("\n--- Register New Student ---")
    name = input("Enter Name: ")
    phone = input("Enter 10-digit Phone: ")
    email = input("Enter Email: ")
    
    is_valid, reason = validate_contact(name, phone, email)
    if not is_valid:
        print(f"Registration Failed: {reason}")
        return
        
    for s in students:
        if s['phone'] == phone.strip():
            print("Registration Failed: Phone number already exists.")
            return
        if s['email'] == email.strip():
            print("Registration Failed: Email address already exists.")
            return
            
    students.append({"name": name.strip(), "phone": phone.strip(), "email": email.strip()})
    save_students_to_csv(students)
    print("Student successfully registered and saved!")

def update_student(students):
    view_all_students(students)
    if not students:
        return
    try:
        choice = int(input("Enter Student ID to update: "))
        if choice < 1 or choice > len(students):
            print("Invalid Student ID.")
            return
    except ValueError:
        print("Please enter a valid numeric ID.")
        return
        
    student = students[choice - 1]
    print(f"Updating record for: {student['name']}")
    new_name = input(f"Enter new name [{student['name']}]: ").strip() or student['name']
    new_phone = input(f"Enter new phone [{student['phone']}]: ").strip() or student['phone']
    new_email = input(f"Enter new email [{student['email']}]: ").strip() or student['email']
    
    is_valid, reason = validate_contact(new_name, new_phone, new_email)
    if not is_valid:
        print(f"Update Failed: {reason}")
        return
        
    students[choice - 1] = {"name": new_name, "phone": new_phone, "email": new_email}
    save_students_to_csv(students)
    print("Student record updated successfully!")

def delete_student(students):
    view_all_students(students)
    if not students:
        return
    identifier = input("Enter Student ID or Phone Number to delete: ").strip()
    
    target_idx = -1
    if identifier.isdigit() and 1 <= int(identifier) <= len(students):
        target_idx = int(identifier) - 1
    else:
        for idx, s in enumerate(students):
            if s['phone'] == identifier:
                target_idx = idx
                break
                
    if target_idx != -1:
        removed = students.pop(target_idx)
        save_students_to_csv(students)
        print(f"Successfully deleted record for {removed['name']}.")
    else:
        print("Student record not found.")

def main():
    # Initial load and clean using embedded contacts.csv data
    students = load_students_from_csv()
    
    while True:
        print("\n=============================================")
        print("   STUDENT REGISTRATION & INTELLIGENCE CONSOLE ")
        print("=============================================")
        print("Option 1 : View All Students")
        print("Option 2 : Search Record")
        print("Option 3 : Register Student")
        print("Option 4 : Update Student")
        print("Option 5 : Delete Student")
        print("Option 6 : Analytics Report")
        print("Option 7 : Exit")
        
        choice = input("Select an option (1-7): ").strip()
        
        if choice == '1':
            view_all_students(students)
        elif choice == '2':
            search_student(students)
        elif choice == '3':
            register_student(students)
        elif choice == '4':
            update_student(students)
        elif choice == '5':
            delete_student(students)
        elif choice == '6':
            generate_analytics(RAW_FILE, students)
        elif choice == '7':
            save_students_to_csv(students)
            print("State saved to students_clean.csv. Exiting program. Goodbye!")
            break
        else:
            print("Invalid option. Please choose between 1 and 7.")

if __name__ == "__main__":
    main()
