# Repository Name Project3
# Organization Name
# Deep Panwala
# Purvesh Kapadiya

import re
from datetime import datetime
from prettytable import PrettyTable

individual_pattern = re.compile(r'0 @I(\d+)@ INDI')
name_pattern = re.compile(r'1 NAME (.+)')
gender_pattern = re.compile(r'1 SEX ([MF])')
birthday_pattern = re.compile(r'1 BIRT\n2 DATE (\d{1,2} [A-Z]{3} \d{4})')
death_pattern = re.compile(r'1 DEAT\n2 DATE (\d{1,2} [A-Z]{3} \d{4})')
family_child_pattern = re.compile(r'1 FAMC @F(\d+)@')
family_spouse_pattern = re.compile(r'1 FAMS @F(\d+)@')

family_pattern = re.compile(r'0 @F(\d+)@ FAM')
marriage_pattern = re.compile(r'1 MARR\n2 DATE (\d{1,2} [A-Z]{3} \d{4})')
divorce_pattern = re.compile(r'1 DIV\n2 DATE (\d{1,2} [A-Z]{3} \d{4})')
husband_pattern = re.compile(r'1 HUSB @I(\d+)@')
wife_pattern = re.compile(r'1 WIFE @I(\d+)@')
child_pattern = re.compile(r'1 CHIL @I(\d+)@')

individuals = {}
families = {}
current_individual = None
current_family = None

def calculate_age(birthdate):
    birthdate = datetime.strptime(birthdate, '%d %b %Y')
    end_date = datetime(2030, 9, 12)
    age = end_date.year - birthdate.year - ((end_date.month, end_date.day) < (birthdate.month, birthdate.day))
    return age



with open("C:\export-Forest.ged", 'r') as gedcom_file:
    lines = gedcom_file.readlines()

for line in lines:
    individual_match = individual_pattern.match(line)
    if individual_match:
        if current_individual:
            individuals[current_individual['identifier']] = current_individual
        current_individual = {
            'identifier': individual_match.group(1),
            'name': None,
            'gender': None,
            'birthday': None,
            'age': None,
            'alive': True,
            'death': None,
            'child': [],
            'spouse': [],
        }

    name_match = name_pattern.match(line)
    if name_match and current_individual:
        current_individual['name'] = name_match.group(1)

    gender_match = gender_pattern.match(line)
    if gender_match and current_individual:
        current_individual['gender'] = 'Male' if gender_match.group(1) == 'M' else 'Female'

    birthday_match = birthday_pattern.match(line)
    if birthday_match and current_individual:
        current_individual['birthday'] = birthday_match.group(1)
        current_individual['age'] = calculate_age(birthday_match.group(1))

    death_match = death_pattern.match(line)
    if death_match and current_individual:
        current_individual['alive'] = False
        current_individual['death'] = death_match.group(1)

    family_child_match = family_child_pattern.match(line)
    if family_child_match and current_individual:
        current_individual['child'].append(family_child_match.group(1))

    family_spouse_match = family_spouse_pattern.match(line)
    if family_spouse_match and current_individual:
        current_individual['spouse'].append(family_spouse_match.group(1))


