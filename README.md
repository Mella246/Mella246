# 🧪 1-р Хэсэг: Аюултай (Unsynchronized) хувилбар
- **`BankSimulationUnsafe.java`**
- Синхрончлолгүй тул balance тогтворгүй гардаг.
- Race condition үүснэ.
class BankAccount {
    private int balance = 1000;

    public synchronized void deposit(int amount) {
        balance += amount;
        System.out.println("Deposited " + amount + ", Balance: " + balance);
    }

    public synchronized void withdraw(int amount) {
        if (balance >= amount) {
            balance -= amount;
            System.out.println("Withdrawn " + amount + ", Balance: " + balance);
        } else {
            System.out.println("Insufficient funds for " + amount);
        }
    }

    public int getBalance() {
        return balance;
    }
}

class Customer implements Runnable {
    private BankAccount account;
    private String action;
    private int amount;

    public Customer(BankAccount account, String action, int amount) {
        this.account = account;
        this.action = action;
        this.amount = amount;
    }

    public void run() {
        if (action.equals("deposit")) {
            account.deposit(amount);
        } else if (action.equals("withdraw")) {
            account.withdraw(amount);
        }
    }
}

public class BankSimulationSynchronized {
    public static void main(String[] args) throws InterruptedException {
        BankAccount account = new BankAccount();
        Thread[] customers = new Thread[3];
        customers[0] = new Thread(new Customer(account, "deposit", 500));
        customers[1] = new Thread(new Customer(account, "withdraw", 700));
        customers[2] = new Thread(new Customer(account, "withdraw", 600));

        for (Thread t : customers) {
            t.start();
        }

        for (Thread t : customers) {
            t.join();
        }

        System.out.println("Final Balance: " + account.getBalance());
    }
}


---

## 🔒 2-р Хэсэг: Синхрончлолтой хувилбар
- **`BankSimulationSynchronized.java`**
- `synchronized` түлхүүр үг ашигласан тул үр дүн тогтвортой.
- Гүйлгээний дараалал өөр байж болно, гэхдээ Final Balance: **200**
class BankAccount {
    private int balance = 1000;

    public void deposit(int amount) {
        balance += amount;
        System.out.println("Deposited " + amount + ", Balance: " + balance);
    }

    public void withdraw(int amount) {
        if (balance >= amount) {
            balance -= amount;
            System.out.println("Withdrawn " + amount + ", Balance: " + balance);
        } else {
            System.out.println("Insufficient funds for " + amount);
        }
    }

    public int getBalance() {
        return balance;
    }
}

class Customer implements Runnable {
    private BankAccount account;
    private String action;
    private int amount;

    public Customer(BankAccount account, String action, int amount) {
        this.account = account;
        this.action = action;
        this.amount = amount;
    }

    public void run() {
        if (action.equals("deposit")) {
            account.deposit(amount);
        } else if (action.equals("withdraw")) {
            account.withdraw(amount);
        }
    }
}

public class BankSimulationUnsafe {
    public static void main(String[] args) {
        BankAccount account = new BankAccount();
        Thread[] customers = new Thread[3];
        customers[0] = new Thread(new Customer(account, "deposit", 500));
        customers[1] = new Thread(new Customer(account, "withdraw", 700));
        customers[2] = new Thread(new Customer(account, "withdraw", 600));

        for (Thread t : customers) {
            t.start();
        }
    }
}


---

## ⚙️ 3-р Хэсэг: ExecutorService ашигласан хувилбар
- **`BankSimulationExecutor.java`**
- `ExecutorService` ашиглан thread-уудыг удирдсан.
- Илүү оновчтой, зохион байгуулалт сайтай хувилбар.
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

class BankAccount {
    private int balance = 1000;

    public synchronized void deposit(int amount) {
        balance += amount;
        System.out.println("Deposited " + amount + ", Balance: " + balance);
    }

    public synchronized void withdraw(int amount) {
        if (balance >= amount) {
            balance -= amount;
            System.out.println("Withdrawn " + amount + ", Balance: " + balance);
        } else {
            System.out.println("Insufficient funds for " + amount);
        }
    }

    public int getBalance() {
        return balance;
    }
}

class Customer implements Runnable {
    private BankAccount account;
    private String action;
    private int amount;

    public Customer(BankAccount account, String action, int amount) {
        this.account = account;
        this.action = action;
        this.amount = amount;
    }

    public void run() {
        if (action.equals("deposit")) {
            account.deposit(amount);
        } else if (action.equals("withdraw")) {
            account.withdraw(amount);
        }
    }
}

public class BankSimulationExecutor {
    public static void main(String[] args) throws InterruptedException {
        BankAccount account = new BankAccount();
        ExecutorService executor = Executors.newFixedThreadPool(2);

        executor.submit(new Customer(account, "deposit", 500));
        executor.submit(new Customer(account, "withdraw", 700));
        executor.submit(new Customer(account, "withdraw", 600));

        executor.shutdown();
        while (!executor.isTerminated()) {
            Thread.sleep(100);
        }

        System.out.println("Final Balance: " + account.getBalance());
    }
}


---

## 🧾 Ашиглалт

1. Java 8+ суулгасан байх
2. Compiler ашиглан ажиллуулах:
    ```bash
    javac BankSimulationUnsafe.java
    java BankSimulationUnsafe
    ```

3. Эсвэл IDE (IntelliJ, Eclipse)-д нээж ажиллуулах

---

## ✅ Хүлээгдэх үр дүн
- Тогтвортой хувилбаруудад Final Balance: **200**
- `Initial: 1000 + Deposit: 500 - Withdraw: 700 - Withdraw: 600 = 200`


<!---
Mella246/Mella246 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
