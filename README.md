# Cash-Split-Application
An application that works to settle a bill in the least number of transactions.

Situation - You and your friends meet often to eat pizza together, however the pizza restaurant only takes cash and not everyone has cash with them every day. You or your friends will always pay for anyone who does not have cash and you keep a record of who paid for who on each day. Not everyone will come to every visit - after a while there is quite a bill to sort out.

Write an algorithm that works out how much you all need to pay each other so everyone pays the correct amount - but note that the bill must always be equalised with the minimum number of transactions.

## Solution

The approach taken to solve this can be summarized as follows:
- When the bill needs to be settled, we calculate the net amount for all friends that involved.
- We pick a friend and perform the necessary transaction to settle this specific friend and re-calculate the net amount for all the friends once again. (Once a friend is settled, his/her net amount will be zero in the next step)
- We recursively perform the previous step until the net amount for all the friends involved is zero.

```python
class Friend:
    """"Friend class represents a friend in the group of friends."""

    # Class attribute where we keep track of all the friends in the group
    list_of_all_friends = []

    def __init__(self, name):
        self.name = name
        self.record = {}

        # Append every instance of a Friend to the class attribute when the friend instance is created
        Friend.list_of_all_friends.append(self)

    # Each friend object has a record of how much to who he/she owes money to
    def owes_amount_to(self, friend, owed_amount: int):
        if friend.name not in self.record:
            self.record[friend.name] = owed_amount
        else:
            self.record[friend.name] += owed_amount

    # Each friend can be identified by a name
    def __str__(self):
        return self.name


# Given the net_amount list which indicates the net amount of a friend instance, we recursively iterate to find the
# minimum number of transactions need to settle the bill
def perform_transactions(net_amount_list):
    # Make sure that a dataset was loaded
    if not net_amount_list:
        print("Dataset not loaded.")
        return 0

    # Obtain the indices of the max and min value of in the net_amount list.
    # net_amount[maxCreditorIndex] indicates that max amount that friend at maxCreditorIndex should be credited
    # net_amount[maxDebtorIndex] indicates that max amount that a friend at maxDebtorIndex should be debited
    # This is the most negative number in net_amount_list
    maxCreditorIndex = net_amount_list.index(max(net_amount_list))
    maxDebtorIndex = net_amount_list.index(min(net_amount_list))

    # If net_amount[maxCreditorIndex] and net_amount[maxDebtorIndex] are 0, then the bill is settled.
    if net_amount_list[maxCreditorIndex] == 0 and net_amount_list[maxDebtorIndex] == 0:
        return 0

    # Find the minimum transaction amount that needs to be transferred. This means that we need to consider the
    # absolute value of net_amount[maxDebtorIndex] and therefore use a negative of this negative number.
    # We could use abs() here too.
    transaction_amount = min(-net_amount_list[maxDebtorIndex], net_amount_list[maxCreditorIndex])

    # We add this amount to the net_amount of the friend at maxDebtorIndex and we subtract the amount from the
    # net_amount of a friend at maxCreditorIndex. This is used to simulate a transaction being made.
    net_amount_list[maxDebtorIndex] += transaction_amount
    net_amount_list[maxCreditorIndex] -= transaction_amount

    print(Friend.list_of_all_friends[maxDebtorIndex].name, "needs to pay", transaction_amount
          , "to", Friend.list_of_all_friends[maxCreditorIndex].name)

    # Recursively find the maxCreditorIndex and maxDebtorIndex and perform a transaction,
    # until net_amount_list[maxCreditorIndex] == 0 and net_amount_list[maxDebtorIndex] == 0
    perform_transactions(net_amount_list)


# Calculate a net_amount[] from the list_of_debts_list
def calc_net_amount(list_of_debt_list):
    # Create an empty list to store the net value of each friend in Friend.list_of_all_friends
    net_amount_list = [0] * len(Friend.list_of_all_friends)

    # Calculate the net amount for each friend. The net amount of each friend is obtained by subtracting all debts
    # of a friend from all the credits.
    # A positive number indicates that he/she is owed and a negative number indicates that he/she owes.
    for index in range(len(Friend.list_of_all_friends)):
        for i in range(len(Friend.list_of_all_friends)):
            net_amount_list[index] += (list_of_debt_list[i][index] - list_of_debt_list[index][i])

    # Perform a transaction once the net_amount_list is calculated
    perform_transactions(net_amount_list)


# Calculate and return a list of list [i][j] where friend i owes amount at [i][j] to friend j
def calc_all_debts_record():
    # Append the debt list of each friend to this list which is to be returned.
    # This is a list of list [[]]
    all_debts_record_to_return = []

    # Iterate through each friend's record attribute to create a debtorRecord[], where all debts to all other friends is
    # specified. When a friend does not owe another friend consider that as 0 owed.
    for a_debtor in Friend.list_of_all_friends:
        debtorRecord = []
        for a_creditor in Friend.list_of_all_friends:
            if a_creditor.name in a_debtor.record:
                debtorRecord.append(a_debtor.record[a_creditor.name])
            else:
                debtorRecord.append(0)  # Append 0 if one friend does not own another friend

        # Append each debtorRecord[] created to the list that should be returned
        all_debts_record_to_return.append(debtorRecord)

    return all_debts_record_to_return


# Sample DataSet 1
def sample_dataset_1():
    A = Friend("A")
    B = Friend("B")
    C = Friend("C")
    D = Friend("D")

    # Visit 1
    C.owes_amount_to(B, 5000)
    D.owes_amount_to(A, 1000)

    # Visit 2
    B.owes_amount_to(C, 2500)
    A.owes_amount_to(C, 1000)

    # Visit 3
    A.owes_amount_to(C, 1000)
    B.owes_amount_to(C, 2500)

    # Visit 4
    A.owes_amount_to(B, 500)

    # Visit 4
    A.owes_amount_to(B, 500)


# Sample DataSet 2
def sample_dataset_2():
    A = Friend("Joe")
    B = Friend("Max")
    C = Friend("John")
    D = Friend("Jill")

    # Visit 1
    A.owes_amount_to(B, 1000)
    D.owes_amount_to(A, 1000)
    B.owes_amount_to(C, 2500)

    # Visit 2
    A.owes_amount_to(C, 1000)
    B.owes_amount_to(C, 2500)

    # Visit 3
    C.owes_amount_to(B, 5000)

    # Visit 4
    A.owes_amount_to(B, 500)
    A.owes_amount_to(C, 1000)


# Sample DataSet 3
def sample_dataset_3():
    A = Friend("A")
    B = Friend("B")
    C = Friend("C")

    # Visit 1
    A.owes_amount_to(B, 200)
    A.owes_amount_to(C, 300)

    # Visit 2
    B.owes_amount_to(C, 600)


""" Run the script by loading a dataset of friends and their transactions """
########################################################################################################################

# Load only ONE dataset at a time
sample_dataset_1()
# sample_dataset_2()
# sample_dataset_3()

########################################################################################################################

# To settle the bill we calculate the debt record of all the friends
# Pass the class's list_of_all_friends attribute
all_debts_record = calc_all_debts_record()
print("\nRecord of all debts:")
print(all_debts_record)

# Calculate the minimum number of transaction needed to settle the bill and print who owes whom and the owed amount
print("\nMinimum number of transactions to be done:")
calc_net_amount(all_debts_record)


```
