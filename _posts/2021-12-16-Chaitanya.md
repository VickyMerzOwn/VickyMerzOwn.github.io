---
layout: post
title: "Home-Work 3, Question 6"
subtitle: "Classify the students in CS100 based on their savings"
---
# Assignment 3

## Question 6
Using `awk`, your task is to classify the students in CS100 based on their savings.

### Instructions
- Write an `awk` one-liner/script to get the list of all students from the group file
- Let us say that `Pocket Money` is fixed to 4000 and the minimum expenditure is 2500. You have to generate a list of 59 random integers between 2500 and 4000 for the field of `Pocket Money`.
- `Savings = Pocket Money - Expenses`
- Output format is `Roll No`, `Name`, `Pocket Money`, `Expenses`, `Savings`, `Category`
- The categories with respect to savings are: Miser, Planner, Lavish (Decide intervals yourself)
- Format the output for column-wise display.
- Show a consolidated report of how many students fall in each category.

The output should look something like this

| Roll No. | Name             | Pocket Money | Expenses | Savings | Category |
|----------|------------------|--------------|----------|---------|----------|
| 12040450 | Chaitanya Bisht  | 4000         | 3500     | 500     | Lavish   |
| 12040450 | Ananya           | 4000         | 3000     | 1000    | Lavish   |
| 12041710 | Satvik Vemuganti | 4000         | 4000     | 0       | Lavish   |


### Bash Script

```bash
#! /bin/bash

expenses=()
category=()
students=$(awk -F "," '{print $2","$3"\n"$4","$5"\n"$6","$7}' group | grep -v "^,")
count=$(echo "$students" | awk -F "\n" 'BEGIN{i=0} {i++} END{print i}')

for i in $(seq "$count")
do
    e=$((2500 + $RANDOM % 1500))
    expenses+=($e)
    if [[ $e -ge 3500 ]]
    then
        category+=("Lavish")
    elif [[ $e -ge 3000 ]]
    then
        category+=("Planner")
    elif [[ $e -lt 3000 ]]
    then
        category+=("Miser")

    fi
done


output=$(echo "$students" | awk -v arr="${expenses[*]}" -v arr2="${category[*]}" -F "," 'BEGIN{i=1;split(arr,list," ");split(arr2,list2," ");print "Roll No.,Name,Pocket Money,Expenses,Savings,Category"} {printf "%d,%s,4000,%d,%d,%s\n",$2,$1,list[i],(4000-list[i]),list2[i];i++}'
)


# 6a
echo "$output" | column -t -s ","


# 6B
echo "$output" | awk -F "," 'BEGIN{lavish=0;miser=0;planner=0}{
    if ($6 == "Lavish") lavish++;
    if ($6 == "Miser") miser++;
    if ($6 == "Planner") planner++;
} END{
    print "\nTotal Counts"
    print "Lavish: " lavish
    print "Planner: " planner
    print "Miser: " miser

}'
```
### Script Explanation
Let us break down the script and understand how it works

```bash
expenses=()
category=()
students=$(awk -F "," '{print $2","$3"\n"$4","$5"\n"$6","$7}' group | grep -v "^,")
count=$(echo "$students" | awk -F "\n" 'BEGIN{i=0} {i++} END{print i}')
```
Here the `expenses` and `category` are empty arrays which will be populated later on. `students` array contains the list of students with their roll numbers.

`count` contains the total number of students enrolled in the CS100 course

---
```bash
for i in $(seq "$count")
do
    e=$((2500 + $RANDOM % 1500))
    expenses+=($e)
    if [[ $e -ge 3500 ]]
    then
        category+=("Lavish")
    elif [[ $e -ge 3000 ]]
    then
        category+=("Planner")
    elif [[ $e -lt 3000 ]]
    then
        category+=("Miser")

    fi
done
```

This `for` loop assigns a random expenditure between 2500 to 4000 to each students and stores the corresponding expenditure to the `expenses` array. At the same time the category of each students is determined and is stored in the `category` array.

---

```bash
output=$(echo "$students" | awk -v arr="${expenses[*]}" -v arr2="${category[*]}" -F "," 'BEGIN{i=1;split(arr,list," ");split(arr2,list2," ");print "Roll No.,Name,Pocket Money,Expenses,Savings,Category"} {printf "%d,%s,4000,%d,%d,%s\n",$2,$1,list[i],(4000-list[i]),list2[i];i++}'
)

echo "$output" | column -t -s ","
```

This one-liner creates a column like formatted information of the students with their expenses, savings and categories.

The `column`  command is used to display the table in a neat and formatted manner and adjusts the data being displayed automatically according to the terminal window size.

---
```bash
echo "$output" | awk -F "," 'BEGIN{lavish=0;miser=0;planner=0}{
    if ($6 == "Lavish") lavish++;
    if ($6 == "Miser") miser++;
    if ($6 == "Planner") planner++;
} END{
    print "\nTotal Counts"
    print "Lavish: " lavish
    print "Planner: " planner
    print "Miser: " miser

}'
```

This `awk` script takes the output of the table and counts how many students belong to each category and at the end displays the total count.

