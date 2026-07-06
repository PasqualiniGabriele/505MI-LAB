# XSS LAB

#### The objective of this lab activity is to complete 2 OWASP's Juice-Shop challenges:
#### 1.  Perform a DOM XSS attack
#### 2.  Perform a reflected XSS attack

## 1. Perform a DOM XSS attack:
### Perform a DOM XSS attack with <iframe src="javascript:alert(`xss`)">

![](images/search_bar.png)

The attack is performed in the search bar

![](images/alert.png)

#### A new element is created:
![](images/new_element.png)

In the html we can see a new iframe with the alert, so the attack is successful and it is possible to exploit this vulnerability.

#### Code vulnerability:
![](images/root_cause.png)