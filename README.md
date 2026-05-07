import json

def save_tasks(tasks, completed_tasks, categories):
    data = {
        'tasks': tasks,
        'completed_tasks': completed_tasks,
        'categories': categories
    }

    with open('to-do-list.json', 'w') as f:
        json.dump(data, f)

def load_tasks():
    try:
        with open('to-do-list.json', 'r') as f:
            data = json.load(f)
            tasks = data['tasks']
            completed_tasks = data['completed_tasks']
            categories = data['categories']

    except (FileNotFoundError, json.JSONDecodeError):
        tasks = []
        completed_tasks = []
        categories = {}

    return tasks, completed_tasks, categories

def view_task(tasks, completed_tasks, categories):
    if not any([tasks, completed_tasks, categories]):
        print('\nNo tasks available\n')
    else:
        if tasks:
            print('\nTasks\n')

            for i, val in enumerate(tasks, start=1):
                print(f'{i}.) {val}')

            print('')

        if categories:
            for k, v in categories.items():
                print(f'{k.title()}')
                for i, val in enumerate(v, start=1):
                    print(f'{i}.) {val}')

                print('')

        if completed_tasks:
            print('Completed Tasks')

            for i, val in enumerate(completed_tasks, start=1):
                print(f'{i}.) {val}')

            print('')

def add_task(tasks, categories):
    new_task = input("Enter a new task: ")

    if not categories:
        tasks.append(new_task)
    else:
        while True:
            choice = input('Press 1 to add task to main task\nPress 2 to add task to categorized tasks:\n')

            if choice.lower() == 'b':
                return

            if choice not in ('1', '2'):
                print('Please enter either 1 or 2 only or (press b to go back to main meny)')
                continue

            break

        if choice == '2':
            while True:
                task_category = input('Enter category to move the task: ').title()

                if task_category.lower() == 'b':
                    return

                found = False

                for k in list(categories):
                    if task_category.lower() == k.lower():
                        categories[k].append(new_task)
                        found = True
                        break

                if not found:
                    print('Please enter a valid category or press b to go back to main menu')
                    continue

                break
        else:
            tasks.append(new_task)

    print('Task added successfully\n')

def remove_task(tasks, completed_tasks, categories):
    if not any([tasks, completed_tasks, categories]):
        print('\nNo task to remove\n')
        return
    else:
        while True:
            try:
                remove_item = int(input(
'''Press 1 to remove a task in main tasks
Press 2 to remove a completed task
Press 3 to remove a task in a category
Press 4 to go back to main menu\n'''))
            except ValueError:
                print('Please enter a valid number')
                continue

            if remove_item not in (1, 2, 3, 4):
                print('Please enter a valid number')
                continue

            if not tasks and remove_item == 1:
                print('No task to remove')
                continue

            if not completed_tasks and remove_item == 2:
                print('No task to remove')
                continue

            if not categories and remove_item == 3:
                print('No task to remove')
                continue

            break

    if remove_item == 3:
        while True:
            category_name = input('Choose an existing category: ')

            if category_name.lower() == 'b':
                return

            found = False
            found_category = None

            for k in list(categories):
                if category_name.lower() == k.lower():
                    found_category = k
                    found = True
                    break

            if not found:
                print('Please enter a valid category or press b to go back to main menu')
                continue

            break

    if remove_item != 4:
        while True:
            removed_task = input("Enter the number of the task to remove: ")

            if removed_task.lower() == 'b':
                return

            try:
                removed_task = int(removed_task)
            except ValueError:
                print("Please enter a valid number (or press b to go back to main menu).")
                continue

            if removed_task - 1 < 0:
                print("Please enter a valid number (or press b to go back to main menu).")
                continue

            try:
                if remove_item == 1:
                    del tasks[removed_task - 1]
                elif remove_item == 2:
                    del completed_tasks[removed_task - 1]
                elif remove_item == 3:
                    del categories[found_category][removed_task - 1]
                elif remove_item == 4:
                    return

                print('Task removed successfully\n')
                return
            except IndexError:
                print("Please enter a valid number (or press b to go back to main menu).")

def mark_complete(tasks, completed_tasks, categories):
    if not tasks and not categories:
        print('\nNo task to complete\n')
        return

    completed_section = None

    if tasks and categories:
        while True:
            completed_section = input('If task completed in main task (Press 1)\nElse (Press 2):\n')

            if completed_section.lower() == 'b':
                return

            if completed_section not in ('1', '2'):
                print('Please enter a valid number (or press b to go back to main menu)')
                continue

    if completed_section == '2' or (categories and not tasks):
        while True:
            completed_category = input('Choose an existing category: ')

            if completed_category.lower() == 'b':
                return

            found = False
            found_category = None

            for k in categories:
                if completed_category.lower() == k.lower():
                    found_category = k
                    found = True
                    break

            if not found:
                print('Please enter a valid category or press b to go back to main menu')
                continue

    while True:
        completed = input("Enter the number of the task completed: ")

        if completed.lower() == 'b':
            return

        try:
            completed = int(completed)
        except ValueError:
            print("Please enter a valid number (or press b to go back to main menu).")
            continue

        if completed - 1 < 0:
            print("Please enter a valid number (or press b to go back to main menu).")
            continue

        try:
            if (not categories and tasks) or completed_section == '1':
                transferred = tasks.pop(completed - 1)
            else:
                transferred = categories[found_category].pop(completed - 1)

            completed_tasks.append(transferred)
            print('Task completed.\n')
            return

        except IndexError:
            print("Please enter a valid number (or press b to go back to main menu).")

def add_category(categories):
    new_category = input('Enter category to add: ').title()
    categories[new_category] = []
    print('Category added successfully')

def remove_category(categories):
    while True:
        old_category = input('Enter category to remove: ')

        if old_category.lower() == 'b':
            return

        found = False

        for key in categories:
            if key.lower() == old_category.lower():
                del categories[key]
                print('Category removed successfully')
                found = True
                break

        if not found:
            print('Please choose a valid category or press b to go back to main menu.')
            continue

        return

def categorize(categories):
    while True:
        if not categories:
            add_category(categories)
            return

        else:
            while True:
                try:
                    add_or_remove = int(input(
                        'Press 1 to add a category\n' +
                        'Press 2 to remove a category\n'
                    ))
                except ValueError:
                    print('Please enter 1 or 2 only.')
                    continue

                if add_or_remove not in (1, 2):
                    print('Please enter 1 or 2 only.')
                    continue

                if add_or_remove == 1:
                    add_category(categories)
                else:
                    remove_category(categories)

                return

def control_task():
    print('''Todo list Menu:
    1. View Tasks
    2. Add a Task
    3. Remove a Task
    4. Mark a Task as Completed
    5. Add or Remove a Category
    6. Exit''')

    while True:
        try:
            choice = int(input("Enter your choice: "))
        except ValueError:
            print("Please enter a valid number.")
            continue

        if choice not in range(1, 7):
            print("Please enter a valid number.")
            continue

        return choice

def main():
    tasks, completed_tasks, categories = load_tasks()

    while True:
        choice = control_task()

        if choice == 1:
            view_task(tasks, completed_tasks, categories)
            continue
        elif choice == 2:
            add_task(tasks, categories)
            continue
        elif choice == 3:
            remove_task(tasks, completed_tasks, categories)
            continue
        elif choice == 4:
            mark_complete(tasks, completed_tasks, categories)
            continue
        elif choice == 5:
            categorize(categories)
        elif choice == 6:
            save_tasks(tasks, completed_tasks, categories)
            break

if __name__ == "__main__":
        main()
