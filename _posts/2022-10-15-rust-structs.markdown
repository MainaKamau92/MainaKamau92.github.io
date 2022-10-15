---
layout: post
title:  "Structs in Rust"
date:   2022-10-15 10:07:47 +0300
categories: jekyll update
---

## Structs
### Definition
Structs are data types in Rust with a `key:value` structure similar to an object in Javascript or a dictionary in python. They are defined using the keyword `struct`, providing a name and colon separated `name: data type` fields separated by commas. An example would look like:

```
struct BankAccount {
    account_name: String,
    account_type: String,
    account_balance: f32,
    bank_branch: String
};
```

### Instantiation
The above code represents the definition of a struct, next we look at how to instantiate the struct. You can instantiate a struct by providing actual values for the specified fields. You need not follow the ordering as during definition, the example below would work just fine:

```
fn main() -> struct {
    let bank_acc = BankAccount {
        account_type: String::from("Checking"),
        bank_branch: String::from("Nairobi CBD"),
        account_balance: 120002.99,
        account_name: String::from("John Doe")
    };
}
```

### Updating and Getting values
In order to fetch a value from a struct you'd make use of dot notation, simlar to retrieving values from an object in Javascript. Continiuing with the ealier example you can retrieve the `account_name` using the code `bank_acc.account_name`, account balance using `bank_acc.account_balance` and so forth.

If the struct instance is mutable you can also update individual values similarly using the dot notation. Attempting this with an immutable (default) instance would throw an error at compilation. Make the struct instance mutable by adding the `mut` keyword and update the `account_name` like so:

```
let mut bank_acc = BankAccount {
        account_type: String::from("Checking"),
        bank_branch: String::from("Nairobi CBD"),
        account_balance: 120002.99,
        account_name: String::from("John Doe")
    };

println!("The account name is {0}", bank_acc.account_name);
bank_acc.account_name = String::from("Jane Doe");
println!("The account name is {0}", bank_acc.account_name);

>>> The account name is John Doe
>>> The account name is Jane Doe
```
>> Note: The entire struct instance must be mutable, it is not possible to make individual fields mutable while others remain immutable

### Return types

You can return a struct instance from a function similar to any other data type. You can also pass the params passed in the function directly to the instance. Consider below example for instance:
```
struct BankAccount {
    account_name: String,
    account_type: String,
    account_balance: f32,
    bank_branch: String
}

fn main() {
    let new_account = create_account(String::from("Mombasa CBD"), 1200.78);
    println!("The account name is {0}", new_account.bank_branch);
}

fn create_account(bank_branch: String, acc_balance: f32) -> BankAccount
{
    BankAccount {
        account_type: String::from("Checking"),
        bank_branch: bank_branch,
        account_balance: acc_balance,
        account_name: String::from("John Doe")
    }
}
```
Note we have to define the struct globally so that it is within scope to act as valid return type for the `create_account` function. You can also pass the params provided in the function directly as values to the instance.
