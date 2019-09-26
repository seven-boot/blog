```java
private Calendar getCalendar() {
    return Calendar.getInstance();
}

。。。

userQuestions.setYear(getCalendar().get(Calendar.YEAR));
userQuestions.setMonth(getCalendar().get(Calendar.MONTH) + 1);
```

