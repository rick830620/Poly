Poly
====

#include<iostream>
#include<string>
#include<cstddef>
#include<cstdlib>
#include<new>
#include<fstream>
using namespace std;

struct poly
{
	int co;       //coefficient
	int terms;    //terms
	poly* next;   //next address
};

class polynomial
{
	public :
		polynomial();
	poly* First ;
	int num;
	
	void destroyPolynomial();
	int getDegree();
	int getDegreeC();
	int getnum();
	int getNonZero();
	string getPoly();
	void set(int c, int n);
	void add(polynomial p);
	void multipies(polynomial p);
};
polynomial::polynomial()
{
	poly base ;
	base.co = 0;
	base.terms = 0;
	First = &base;
	base.next = NULL;
	num = 0;
}

void polynomial::destroyPolynomial()
{
	poly* cur; 
	poly* prev;
	while(First != NULL)
	{
		cur = First;
		while(cur->next != NULL)
		{
			prev = cur;
			cur = cur->next;
		}
		prev->next = cur->next;
		delete cur;
		cur = NULL;
	}	
}
int polynomial::getDegree()
{
	return First->terms;
}
int polynomial::getDegreeC()
{
	return First->co;
}
int polynomial::getnum()
{
	return num;
}
int polynomial::getNonZero()
{
	
	int num = 0;
	poly* cur ;
	cur = First;
	
	while(true)
	{
		if (cur == NULL)
			break;
		else if(cur->terms != 0 && cur->co != 0)
			num += 1;
			
		cur = cur->next;
	}
	return num;
	

}
string polynomial::getPoly()
{
	poly* cur = First;
	string s ;
	char str[50] ;
	/*strstream ss;
	string str;
	ss << cur->co;
	str = ss.str();
	s = str;*/
	s = itoa(cur->co , str , 10) + 'X' + '^';

	s += itoa(cur->terms , str , 10);
	return s;
	/*s += "X^";
	ss << cur->terms;
	str = ss.str();
	s += str;
	
	cur = cur->next;
	while(cur != NULL)
	{
		if (cur-> co != 0)
		{
			s += "+";
			ss << cur->co;
			str = ss.str();
			s += str;
			s += "X^";
			ss >> cur->terms;
			str = ss.str(); 
			s += str;
				
		}
		
		cur = cur->next;
	}
	return s;*/
}
void polynomial::set(int c, int n)
{
	if (c != 0)
	{
		num += 1;
		poly newpoly ;
		newpoly.co = c;
		newpoly.terms = n;
		newpoly.next = NULL;
		poly* cur;
		poly* prev;
		cur = First;
		prev = NULL;
		if (getDegree() < newpoly.terms)
		{
			newpoly.next = First;
			First = &newpoly;
		}
		else
		{
			while(cur->terms > newpoly.terms && cur != NULL)
			{
				prev = cur;
				cur = cur->next;
			}
			if (cur->terms == newpoly.terms)
				cur->co += newpoly.co;
			else
			{
				newpoly.next = cur;
				prev->next = &newpoly;
			}	
		}
	}
}
void polynomial::add(polynomial p)
{
	poly* temp;             
	temp = p.First;
	poly* cur;
	cur = First;
	poly* prev;
	while (temp != NULL)
	{
		while(cur->terms > temp->terms)
		{
			prev = cur;
			cur = cur->next;
		}
		if (cur->terms = temp->terms)
			cur->co += temp->co;
		else
			{
				temp->next = cur;
				prev->next = temp;
			}
		temp = temp->next;
	}	
}
void polynomial::multipies(polynomial p)
{
	polynomial ptr;     //target
	poly* temp1;        //p1
	poly* temp2;        //p2
	temp1 = First;
	temp2 = p.First;
	int c = 0;
	int n = 0;
	while(temp1 != NULL)
	{
		while(temp2 != NULL)
		{
			c = temp1->co + temp2->co;
			n = temp1->terms + temp2->terms;
			ptr.set(c , n);
			temp2 = temp2->next;
		}	
	}
	First = ptr.First;
	ptr.destroyPolynomial();
}




int main(int argc , char* argv[])
{
	ifstream fin;
	ofstream fout;
	string temp;
	
	if (argc == 2)
		fin.open(argv[1]);
	else{ 
        cout << "Please input file name: ";
        cin >> temp;
        // 開檔 input1.txt
        fin.open(temp.c_str()); // c_str() : string to char string
    }
    
    //- 若開檔 input1.txt 失敗，可能是檔案不存在。
    if (fin.fail()){ 
        cout << "Fail to open file\n";
        exit(1); // 非正常結束程式
    } 

    // 建新檔 output1.txt，預設為覆蓋。
    fout.open("output1.txt"); 

    //- 若開檔 output1.txt 失敗
    if (fout.fail()){ 
        cout << "Fail to open file\n";
        exit(1); // 非正常結束程式
    }
	char m = ' ';
	int cal_num = 0;
	char x,y;
	int c;
	int n;
	int poly_num = 1;
	polynomial a , b ;
	string poly ;
	int degree = 0;
	int nonzero = 0;
	
	while(true)
	{
		m = fin.get();
		if (m == '+' || m == '*')
		{
			if (poly_num == 1)
			{
				x = m;
				poly = a.getPoly();
				degree = a.getDegree();
				nonzero = a.getNonZero();
				fout << "Polynomial " << poly_num << ":" << poly << endl;
				fout << "Degree:" << degree << endl;
				fout << "# of nonzero var: " << nonzero << endl;
				fout << endl;
				
			}
			else 
			{
				poly = b.getPoly();
				degree = b.getDegree();
				nonzero = b.getNonZero();
				fout << "Polynomial " << poly_num << ":" << poly << endl;
				fout << "Degree:" << degree << endl;
				fout << "# of nonzero var: " << nonzero << endl;
				fout << endl;
				if (poly_num % 2 == 0)
				{
					y = m;
					if (x == '+')
						a.add(b);
					else
						a.multipies(b);
					b.destroyPolynomial();
				}
				else 
				{
					x = m;
					if (y == '+')
						a.add(b);
					else
						a.multipies(b);
					b.destroyPolynomial();
				}
			}
			cal_num += 1;
			poly_num += 1;
		}
		else
		{
			fin.putback(m);
			fin >> c >> n;
			
			if (poly_num == 1)
			{
				a.set(c , n);
				fout << c << endl;
				fout << a.getDegreeC() << endl;	
				fout << a.getDegree() << endl;	
				fout << a.getnum() << endl;
			}	
			else
				b.set(c , n);
			fin.get();
		}
		if (fin.eof())
		{
			poly = b.getPoly();
			degree = b.getDegree();
			nonzero = b.getNonZero();
			fout << "Polynomial " << poly_num << ":" << poly << endl;
			fout << "Degree:" << degree << endl;
			fout << "# of nonzero var: " << nonzero << endl;
			fout << endl;
			if (poly_num % 2 == 0)
			{
				y = m;
				if (x == '+')
					a.add(b);
				else
					a.multipies(b);
			}
			else 
			{
				x = m;
				if (y == '+')
					a.add(b);
				else
					a.multipies(b);
			}
			poly = a.getPoly();
			degree = a.getDegree();
			nonzero = a.getNonZero();
			fout << "Polynomial " << poly_num << ":" << poly << endl;
			fout << "Degree:" << degree << endl;
			fout << "# of nonzero var: " << nonzero << endl;
			fout << endl;
			
			b.destroyPolynomial();
			a.destroyPolynomial();
		}
	}
	
	fin.close();
	fout.close();
}



