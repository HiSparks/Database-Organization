---
title: "Лекция 12. Вычисления в DataSet и выполнение DML-операций в разъединенном окружении"
layout: bookpage
lang: ru
navigation_weight: 12
--- 

# Лекция 12. Вычисления в DataSet и выполнение DML-операций в разъединенном окружении

## 1. Вычисления в DATASET

Вычисления над данными таблиц БД:

- путем непосредственных вычислений над значениями данных таблицы в наборе данных DataSet,
- с помощью SQL–запросов над таблицей БД,
- путем вычислений над значениями данных “сетки” – элемента DataGridView.

**Пример**. Создание вычислимого поля в таблице БД.

Пусть в таблице есть поле `Year_b` ("Год рождения"). Необходимо cоздать новый столбец с заголовком "Возраст", добавить его в таблицу, заполнить его данными (вычисление возраста по году рождения) и показать новую таблицу.

```csharp

//кнопка "Возраст" - создание дополнительного поля "Возраст"
private void age_Click(object sender, EventArgs e)
{
	DataColumn dc = new DataColumn();// создание столбца
	dc.Caption = "Возраст"; // заголовок поля
	dc.ColumnName = "NumberYear";// имя поля
	
	// тип данных элементов столбца
	dc.DataType = System.Type.GetType("System.Int32");
	dc.Unique = false;
	dc.DefaultValue = 20; // значение по умолчанию
	
	// добавить столбец в таблицу
	ds.Tables[0].Columns.Add(dc);

	//вычисление возраста и вставка его в строки нового столбца
	for (int i = 0; i < ds.Tables[0].Rows.Count; i++)
		ds.Tables[0].Rows[i]["NumberYear"] = (DateTime.Today.Year) - Convert.ToInt32(ds.Tables[0].Rows[i]["Year_b"]);
		
	//подключение к сетке измененной таблицы
	dGV.DataSource = ds.Tables[0];
	
	// задание заголовка нового столбца в сетке
	dGV.Columns["NumberYear"].
	HeaderText = dc.Caption;
}

```

(pic)

**Пример.** На основании таблицы в DataSet выполнить следующие вычисления:

- определить средний возраст студентов в таблице;
- определить средний бал по математике;
- определить средний балл по Math для группы '525'.

```csharp 

// кнопка "Вычислить" - вычисления над данными БД
private void compute_Click(object sender, EventArgs e) {

	//вычисление среднего возраста студентов в таблице
	double sr = 0;
	DataRow row; // ds объект класса DataSet

	for (int i = 0; i < ds.Tables[0].Rows.Count; i++) {
		row = ds.Tables[0].Rows[i];
		// возраст в ячейке под номером 9
		sr = sr + Convert.ToDouble(row["NumberYear"]);
	}

	label2.Text = "Средний возраст = ";

	// использование форматирования при выводе
	label2.Text += String.Format("{0:f2}", (sr / ds.Tables[0].Rows.Count));

	// средний бал по математике
	sr = 0;

	for (int i = 0; i < ds.Tables[0].Rows.Count; i++) {
		row = ds.Tables[0].Rows[i];
		sr = sr + Convert.ToDouble(row["Math"]);
	}

	label2.Text += " Средний бал по Math = ";

	label2.Text += String.Format("{0:f2}", (sr / ds.Tables[0].Rows.Count)); // вычисление среднего балла по Math для группы '525'

	// с помощью метода Compute класса DataTable
	object objAvg = ds.Tables[0].Compute("Avg(Math)","Group = '525'");

	label3.Text =" Средней балл 525 группы по Math= " +
	String.Format("{0:f2}",Convert.ToDouble(objAvg));
}

```