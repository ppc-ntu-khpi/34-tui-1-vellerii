# UI Lab 1

## Виконання завдання на "п'ять"
**В ході виконнаня практичної роботи потрібно було зробити наступне:**
* переписати метод ShowCustomerDetails з використанням класів Bank, Customer, Account та ін.;
* інформація про клієнтів банку та їх рахунках має читатись з файлу test.dat;
* інформацію про клієнта (та про перший рахунок, що йому належить) ви маєте побачити, увівши номер клієнта.
---
**Програмний код класу `TUIdemo` наведений нижче.**

```java
package com.mybank.tui;
import com.mybank.data.DataSource;
import com.mybank.domain.*;

import jexer.TAction;
import jexer.TApplication;
import jexer.TField;
import jexer.TText;
import jexer.TWindow;
import jexer.event.TMenuEvent;
import jexer.menu.TMenu;

/**
 *
 * @author Alexander 'Taurus' Babich
 */
public class TUIdemo extends TApplication {

    private static final int ABOUT_APP = 2000;
    private static final int CUST_INFO = 2010;

    public static void main(String[] args) throws Exception { 
        DataSource dataSource = new DataSource("/Users/vkrychevska/NetBeansProjects/TUIdemo/data/test.dat");
        dataSource.loadData();
        TUIdemo tdemo = new TUIdemo();
        (new Thread(tdemo)).start();
    }

    public TUIdemo() throws Exception {
        super(BackendType.SWING);

        addToolMenu();
        //custom 'File' menu
        TMenu fileMenu = addMenu("&File");
        fileMenu.addItem(CUST_INFO, "&Customer Info");
        fileMenu.addDefaultItem(TMenu.MID_SHELL);
        fileMenu.addSeparator();
        fileMenu.addDefaultItem(TMenu.MID_EXIT);
        //end of 'File' menu  

        addWindowMenu();

        //custom 'Help' menu
        TMenu helpMenu = addMenu("&Help");
        helpMenu.addItem(ABOUT_APP, "&About...");
        //end of 'Help' menu 

        setFocusFollowsMouse(true);
        //Customer window
        ShowCustomerDetails();
    }

    @Override
    protected boolean onMenu(TMenuEvent menu) {
        if (menu.getId() == ABOUT_APP) {
            messageBox("About", "\t\t\t\t\t   Just a simple Jexer demo.\n\nCopyright \u00A9 2019 Alexander \'Taurus\' Babich").show();
            return true;
        }
        if (menu.getId() == CUST_INFO) {
            ShowCustomerDetails();
            return true;
        }
        return super.onMenu(menu);
    }

    private void ShowCustomerDetails() {
        TWindow custWin = addWindow("Customer Window", 2, 1, 40, 10, TWindow.NOZOOMBOX);
        custWin.newStatusBar("Enter valid customer number and press Show...");

        custWin.addLabel("Enter customer number: ", 2, 2);
        TField custNo = custWin.addField(24, 2, 3, false);
        TText details = custWin.addText("Owner Name: \nAccount Type: \nAccount Balance: ", 2, 4, 38, 8);
        custWin.addButton("&Show", 28, 2, new TAction() {
            @Override
            public void DO() {
                try {
                    int custNum = Integer.parseInt(custNo.getText());
                    Customer customer = Bank.getCustomer(custNum);
                    Account account = customer.getAccount(0);
                    String accountType;
                    
                    if (account instanceof CheckingAccount){
                        accountType = "Checking";
                    } else if (account instanceof SavingsAccount) {
                        accountType = "Savings";
                    } else {
                        accountType = "Undefined";
                    }

                    details.setText(
                            "Owner Name: " + customer.getFirstName() + " " + customer.getLastName() + 
                            "\nAccount Type: " + accountType + 
                            "\nAccount Balance: $" + account.getBalance()
                    );
                } catch (Exception e) {
                    messageBox("Error", "You must provide a valid customer number!").show();
                }
            }
        });
    }
}
```
---

**Скріншоти виконання програми наведені нижче.**

![](https://github.com/ppc-ntu-khpi/34-tui-1-vellerii/blob/master/first%20execution.png "1st result")

![](https://github.com/ppc-ntu-khpi/34-tui-1-vellerii/blob/master/second%20execution.png "2snd result")
