# labka
#include "stdafx.h"
#include <string.h>
#include <cstdlib>
#include <iostream>
#include <conio.h>
#include <locale>
using namespace std;


template <class T> 

class TVector
{
	T* vector; 
	int count; 
public:
	int length() const  
	{
		return count; 
	}

	TVector(int qtty) //конструктор, заполняющий нулями
	{
		vector = new T[qtty];
		count = qtty;
		for (auto i(0); i < qtty; i++)
		{
			vector[i] = 0;
		}
	}

	~TVector()  //деструктор
	{ 
		delete[]vector; 
	} 

	TVector(const TVector &obj) //конструктор копирования
	{
		vector = new T[obj.count]; 
		count = obj.count;
		for (auto i(0); i < count; i++)
			vector[i] = obj.vector[i];
	}

	T &operator[](const int index) //оператор скобочки
	{
		if (index < count && index >= 0) 
			return vector[index]; 
		else
			throw EInvalidIndex(index); 
	}

	TVector& operator=(const TVector &vec) //оператор присваивания
	{
		delete[] vector;
		count = vec.count; 
		vector = new T[count];
		for (auto i(0); i < count; i++)
			vector[i] = vec.vector[i];
		return *this;
	}

	TVector operator+(const TVector &vec2) //оператор плюса
	{
		if (count == vec2.count) 
		{
			TVector<T> res = TVector<T>(count); 
			for (auto i(0); i < res.count; i++)
				res[i] = vector[i] + vec2.vector[i]; 
			return res;
		}
		else
			throw EInvalidArg(); 
	}

	TVector operator-(const TVector &vec2) 
	{
		if (count == vec2.count)
		{
			TVector<T> res = TVector<T>(count);
			for (auto i(0); i < res.count; i++)
				res[i] = vector[i] - vec2.vector[i];
			return res;
		}
		else
			throw EInvalidArg();
	}

	TVector operator/(const double num) //деление на число
	{
		TVector<T> res = TVector<T>(count);
		for (auto i(0); i < count; i++)
			res[i] = vector[i] / num;
		return res;
	}

	TVector& operator*=(const T num) //умножение вектора на число
	{
		for (auto i(0); i < this->count; i++)
			vector[i] *= num;
		return *this;
	}

	friend TVector operator*(const T number, const TVector &vec) //умножение числа на вектор
	{
		TVector res = vec;
		for (auto i(0); i < vec.length(); i++)
			res[i] = res[i] * number;
		return res;
	}

	bool operator==(const TVector &vec) //оператор сравнения сравнивает почленно если длины равны.
	{
		if (this->count == vec.count)
		{
			for (auto i(0); i < count; i++)
				if (vector[i] != vec.vector[i])
					return false;
			return true;
		}
		else
			throw EInvalidArg();
	}

	bool operator!=(const TVector &vec) //возвращает отрицание результата предыдущей функции.
	{
		return !(*this == vec);
	}

	void get_values() 
	{
		for (auto i(0); i < count; i++)
		{
			cout << "Введите элемент с индексом [" << i << "] ";
			cin >> vector[i];
			cout << endl;
		}
	}

	void show() const 
	{
		for (int i(0); i < count; i++)
			cout << (double)vector[i] << " ";
	}
};


class EMyException
{
protected:
	char* msg; //сообщение об ошибке
public:
	EMyException(char* message) //конструктор
	{
		msg = _strdup(message); 
	}

	~EMyException() //деструктор
	{
		free(msg); 
	}

	EMyException(const EMyException &obj) //конструктор копирования
	{
		msg = _strdup(obj.msg); 
	}

	virtual void Mssg() //вывод сообщения
	{ 
		puts(msg); 
	}
};


class EInvalidArg : public EMyException  //ошибка, разной длины векторы
{
public:
	EInvalidArg() : EMyException("Разное количество элементов в векторе!") {} //конструктор
	EInvalidArg(const EInvalidArg &src) : EMyException(src) {} //конструтор копирования
};

class EInvalidIndex : public EMyException  //ошибка, нет индекса
{
protected:
	int ind; //индекс
public:
	EInvalidIndex(int Ind) :  //вызов конструктора предка
		EMyException("Нет такого индекса!") 
	{
		ind = Ind; 
	}

	EInvalidIndex(const EInvalidIndex &src) : //конструктор копирования
		EMyException(src)
	{
		ind = src.ind;
	}

	virtual void Print()  //вывод сообщения
	{
		printf("Индекс <%d> вышел за диапазон", ind); 
	}
};


int main() 
{
	int c = 0; 
	setlocale(LC_ALL, "russian");
	try
	{
		int n;
		cout << "Выберите тип вектора.\n1-целочисленный.\n2-вещественный\n";
		cin >> n;
		int l1, l2;
		system("cls");
		if (n == 1)
		{
			system("cls");
			cout << "Введите длину вектора 1\n";
			cin >> l1;
			cout << "Введите длину вектора 2\n";
			cin >> l2;
			TVector<int>fv = TVector<int>(l1);
			TVector<int>sv = TVector<int>(l2);
			cout << "Введите элементы 1 вектора\n";
			fv.get_values();
			cout << "Введите элементы 2 вектора\n";
			sv.get_values();
			char key;
			cout << "Esc - выход\nНажмите любую клавишу.\n";
			system("cls");

			while ((key = _getch()) != 27)
			{
				cout << "\n1 - операция присваивания"
					"\n2-обращение по индексу\n3-сложение\n4-вычитание"
					"\n5-умножение числа на вектор\n6-деление\n7-'==' & '!='\n8- умножение вектора на число\n";
				scanf_s("%i", &c);
				switch (c)
				{
				case 1:
					system("cls");
					fv = sv;
					fv.show();
					break;
				case 2:
					system("cls");
					int ind;
					cout << "Индекс ";
					cin >> ind;
					cout << fv[ind];
					break;
				case 3:
				{
					system("cls");
					auto vec = fv + sv;
					vec.show();
					break;
				}
				case 4:
				{
					system("cls");
					auto vec = fv - sv;
					vec.show();
					break;
				}		
				case 5:
					system("cls");
					cout << "Введите число\n";
					cin >> n;
					fv = n*fv;
					fv.show();
					break;
				case 6:
					system("cls");
					int mem;
					cout << "Введите число\n";
					cin >> mem;
					fv = sv / mem;
					fv.show();
					break;
				case 7:
					system("cls");
					cout << (fv == sv) << (fv != sv);
					break;
				case 8:
					system("cls");
					cout << "Введите число\n";
					cin >> n;
					fv *= n;
					fv.show();
					break;
				}
			}
		}
		else
		{
			cout << "Введите длину вектора 1\n";
			cin >> l1;
			cout << "Введите длину вектора 2\n";
			cin >> l2;
			TVector<double>fv = TVector<double>(l1);
			TVector<double>sv = TVector<double>(l2);
			cout << "Введите элементы 1 вектора\n";
			fv.get_values();
			cout << "Введите элементы 2 вектора\n";
			sv.get_values();
			char key;
			cout << "Esc - выход\nНажмите любую клавишу.\n";
			system("cls");

			while ((key = _getch()) != 27)
			{
				cout << "\n1 - операция присваивания"
					"\n2-обращение по индексу\n3-сложение\n4-вычитание"
					"\n5-умножение числа на вектор\n6-деление\n7-'==' & '!='\n8- умножение вектора на число\n";
				scanf_s("%i", &c);
				switch (c)

				{
				case 1:
					system("cls");
					fv = sv;
					fv.show();

					break;

				case 2:
					system("cls");
					int ind;
					cout << "Индекс ";
					cin >> ind;
					cout << fv[ind];

					break;

				case 3:
				{
					system("cls");
					auto vec = fv + sv;
					vec.show();

					break;
				}

				case 4:
				{
					system("cls");
					auto vec = fv - sv;
					vec.show();

					break;
				}		

				case 5:
					system("cls");
					double num;
					cout << "Введите число\n";
					cin >> num;
					fv = num*fv;
					fv.show();
					break;

				case 6:
					system("cls");
					double mem;
					cout << "Введите число\n";
					cin >> mem;
					fv = sv / mem;
					fv.show();
					break;

				case 7:
					system("cls");
					cout << (fv == sv) << (fv != sv);
					break;

				case 8:
					system("cls");
					cout << "Введите число\n";
					cin >> n;
					fv *= n;
					fv.show();
					break;
				}
			}
		}
		system("pause");
	}
	catch (EMyException &e)
	{
		e.Mssg();
		system("pause");
	}
	return 0;
}
