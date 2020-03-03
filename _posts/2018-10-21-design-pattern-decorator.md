---
layout: post
title:  "Decorator Pattern"
date:   2018-10-21
author: "Huang Qiang"
tags: [java, design pattern]
comments: true
---

```java

public abstract class Pizza {

	String description = "Basic Pizza";
  
	public String getDescription() {
		return description;
	}
 
	public abstract double cost();
}

public class ThincrustPizza extends Pizza {
  
	public ThincrustPizza() {
		description = "Think crust pizza, with tomato sauce";
	}
  
	public double cost() {
		return 7.99;
	}
}

public abstract class ToppingDecorator extends Pizza {
	Pizza pizza;
	public abstract String getDescription();
}

public class Cheese extends ToppingDecorator {
	
	public Cheese(Pizza pizza) {
		this.pizza = pizza;
	}
 
	public String getDescription() {
		return pizza.getDescription() + ", Cheese";
	}
 
	public double cost() {
		return pizza.cost(); // cheese is free
	}
}

public class Olives extends ToppingDecorator {
	
	public Olives(Pizza pizza) {
		this.pizza = pizza;
	}
 
	public String getDescription() {
		return pizza.getDescription() + ", Olives";
	}
 
	public double cost() {
		return pizza.cost() + .30; 
	}
}

public class PizzaStore {
 
	public static void main(String args[]) {
		Pizza pizza = new ThincrustPizza();
		Pizza cheesePizza = new Cheese(pizza);
		Pizza greekPizza = new Olives(cheesePizza);

		System.out.println(greekPizza.getDescription() 
				+ " $" + greekPizza.cost());
	}
}
```


