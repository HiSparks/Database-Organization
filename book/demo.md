---
title: "Демонстрация возможностей"
layout: bookpage
lang: ru
---

# Демонстрация возможностей

## Markdown разметка

### **Типографика:**

**Bold**
*Italic* 
***ItalicBold***

<br>

### **Списки:**

- option 1
	- option 1.1
		- option 1.1.1
		- option 1.1.2
	- option 1.2
- option 2

<br>

### **Нумерованые списоки:**

1. option 1
	1. option 1.1
		1. option 1.1.1
		2. option 1.1.2
	2. option 1.2	
2. option 2

<br>

### **Цитаты:**

> В учении нельзя останавливаться.
> Сюнь-цзы

<br>

### **Вставки кода и вставки с подсведкой синтаксиса:**

Строчные вставки кода `int i = 1;`

```cpp
class Figure
{
	public:
	    virtual void Draw() = 0; // чистый виртуальный метод
	    virtual ~Figure();       // при наличии хотя бы одного виртуального метода деструктор следует сделать виртуальным
};

class Square : public Figure
{
	public:
    	void Draw() override;
};

class Circle : public Figure
{
	public:
	    void Draw() override;
};
```

<br>

### **Изображения:**

![Покемон Пикачу](https://assets.pokemon.com/assets/cms2/img/pokedex/full/025.png)

<br>

### **Формулы, поддержка LaTex:**

$$
	\frac { d V _ { ( x , y , z ) } } { d t } = \frac { \partial V _ { ( x , y , z ) } } { \partial x } V _ { x } + \frac { \partial V _ { ( x , y , z ) } } { \partial y } V _ { y } + \frac { \partial V _ { ( x , y , z ) } } { \partial z } + \frac { \partial V _ { ( x , y , z ) } } { \partial t }.\tag{1.1}
$$

<br>

### **Выполнение кода в браузере:**

<iframe height="400px" width="100%" src="https://repl.it/repls/AverageSilkyProprietarysoftware?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

<br>

### **Контрольные вопросы:**

1.	Определение базы данных.
	<div class="question__answer" markdown="1">
	
	***База данных*** — представленная в объективной форме совокупность самостоятельных материалов (статей, расчётов, нормативных актов, судебных решений и иных подобных материалов), систематизированных таким образом, чтобы эти материалы могли быть найдены и обработаны с помощью электронной вычислительной машины (ЭВМ).
	
	</div>

2.	Классификация баз данных по структуре организации данных.
	<div class="question__answer" markdown="1">

	В зависимости от вида организации данных различают следующие модели БД:
	
	- иерархическую,
	- сетевую,
	- объектно- ориентированную,
	- реляционную.

	</div>

<br>

### **Графики функций:**
	
<iframe src="https://mdleducation.github.io/grapher/?latexList=%5B%22z%3D7%5C%5Ccdot%20x%5C%5Ccdot%5C%5Cfrac%7By%7D%7Be%7D%5E%7B%20%7D%5C%5Cleft(x%5E2%2By%5E2%5C%5Cright)%22%5D#settings-modal" width="100%" height="500px" style="border: 1px solid #ccc" frameborder="0" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

*http://grapher.mathpix.com*

<br>

### **3D модели:**

<iframe src="https://mdleducation.github.io/Database-Organization/demo/webgl_loader_collada.html" width="100%" height="500px" style="border: 1px solid #ccc" frameborder="0" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

*THREE JS lib, .dae - 3d object*