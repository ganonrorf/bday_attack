import hashlib
import itertools

n = 2 #can change to whatever u like

# gets the hash of the OG file
def calculate_sha256_hash(file_path):
    with open(file_path, 'rb') as f:
        content = f.read()
    return hashlib.sha256(content).hexdigest()

# adds spaces to end of line
def add_spaces(lines, combination):
    modified_lines = [
        line.rstrip('\n') + ' ' * spaces + '\n'  
        for line, spaces in zip(lines, combination)
    ]
    return modified_lines

def save_new(lines, file_path):
    with open(file_path, 'w') as f:
        f.writelines(lines)

def birthday_attack(original_file, fake_file, output_file):
    original_hash = calculate_sha256_hash(original_file)
    original_last_n_digits = original_hash[-n:]
    print(f"Original file hash: {original_hash} (last n digits: {original_last_n_digits})")

    with open(fake_file, 'r') as f:
        fake_lines = f.readlines()

    num_lines = len(fake_lines)

    for combination in itertools.product(range(2), repeat=num_lines):
        modified_fake_lines = add_spaces(fake_lines, combination)

        save_new(modified_fake_lines, output_file)

        # computes the hash of the modified fake file
        fake_hash = calculate_sha256(output_file)
        fake_last_n_digits = fake_hash[-n:]

        # checks for a match
        if fake_last_n_digits == original_last_n_digits:
            print(f"Match found, modified fake saved to {output_file}")
            print(f"Fake hash: {fake_hash} (last {n} digits: {fake_last_n_digits})")
            return

    print("No match found.")

birthday_attack('original.txt', 'fake.txt', 'modified_fake.txt')
