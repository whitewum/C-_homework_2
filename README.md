# C-_homework_2
# C++课程作业（二）

## 第九章

### 9.11

#### 1、`vector<int> v` 此时vector容器中没有元素

#### 2、`vector<int> v(10)` 此时vector容器中有10个`int` 大小的空间但值为零

#### 3、`vector<int> v(5, 2)`此时vector容器中有5个值为2的`int` 类型整数

#### 4、`vector<int> v{1, 2, 3, 4, 5}` 此时vector容器中有1，2， 3， 4， 5五个整数

#### 5、`vector<int> v(vec)` 括号中接受另一个vector容器，将`vec` 容器中的值拷贝到`v` 容器中

#### 6、`vector<int> v(vec.begin(), vec.begin() + 2)` 括号中接受两个同一个容器的两个迭代器，将这两个迭代器范围的元素拷贝到`v` 容器中（区间为左闭右开）

### 9.25

#### 1、当`elem1` 和`elem2` 相等时，不会发生删除操作

#### 2、当`elem2` 是尾后迭代器时，会删除从第一个迭代器参数开始到容器最后一个元素 

#### 3、当`elem1` 和 `elem2` 都是尾后迭代器时，不会发生删除操作

### 9.29

#### `resize()` 函数改变容器的大小，多删少补并初始化，需要默认初始化的，则需要参数类型有默认构造函数，`resize(100)` 可以将容器大小改为100个元素的大小，添加新的元素并进行初始化，若再使用`resize(10)` 会将之后的90个元素删除

### 9.43

```c++
//编写函数，接受三个string参数s，oldVal和newVal使用迭代器及insert和erase函数将s中所有oldVal替换为newVal。
#include<iostream>
#include<algorithm>
#include<string>
using namespace std;
void exchange(string &s, string &oldval, string &newval) {
	int len1 = oldval.size();
	int len2 = newval.size();
	auto it2 = newval.begin();
	auto it3 = newval.end();
	for (auto it = s.begin(); it <= s.end() - len1 + 1; ++it) {
		if (s.substr(it - s.begin(), len1) == oldval) {
			string::iterator it1 = s.erase(it, it + len1);//返回删除后的迭代器的位置
			it1 = s.insert(it1, it2, it3);
			it1 += len2 - 1;//使迭代器跳过替代的字符串
		}
	}
}
int main(void) {
	string s = "abglddagjaddadfadd";
	string oldval = "dd";
	string newval = "kld";
	exchange(s, oldval, newval);
	cout << s << endl;
	return 0;
}
```

### 9.52

```c++
//使用stack处理括号表达式。当你看到一个左括号，将其记录下来。当你在一个左括号之后看到一个右括号，从stack中pop对象，直至遇到左括号，将左括号也一起弹出栈。然后将一个值（括号内的运算结果）push到栈中，表示一个括号化的表达式已经处理完毕，被其运算结果所替代
#include<iostream>
#include<string>
#include<deque>
#include<stack>
#include<stdexcept>
using namespace std;
enum obj_type {
	LP, RP, ADD, SUB, VAL
};
struct obj {
	obj_type t;
	double v;
	obj(obj_type type, double val = 0) {
		t = type;
		v = val;
	}
};
inline void skip(string &exp, size_t &p) {
	p = exp.find_first_not_of(" ", p);
}
inline void new_val(stack<obj> &so, double v) {
	if (so.empty() || so.top().t == LP) { 
		so.push(obj(VAL, v));
	}
	else if (so.top().t == ADD || so.top().t == SUB) {
		obj_type type = so.top().t;
		so.pop();
		if (type == ADD) {
			v += so.top().v;
		}
		else {
			v = so.top().v - v;
		}
		so.pop();
		so.push(obj(VAL, v));
	}
	else throw invalid_argument("缺少运算符");
}
int main(void) {
	stack<obj> so;
	string exp;
	size_t p = 0, q;
	double v;
	cout << "请输入表达式 ";
	getline(cin, exp);
	while (p < exp.size()) {
		skip(exp, p);
		if (exp[p] == '(') {
			so.push(obj(LP));
			p++;
			cout << "push LP" << endl;
		}
		else if (exp[p] == '+' || exp[p] == '-') {
			if (so.empty() || so.top().t != VAL)
				throw invalid_argument("缺少运算数");
			if (exp[p] == '+') 
				so.push(obj(ADD));
			else so.push(obj(SUB));
			p++;
		}
		else if (exp[p] == ')') { 
			p++;
			if (so.empty()) 
				throw invalid_argument("未匹配的右括号");
			if (so.top().t == LP) 
				throw invalid_argument("空括号");
			if (so.top().t == VAL) { 
				v = so.top().v;
				so.pop();
				if (so.empty() || so.top().t != LP) {
					throw invalid_argument("未匹配右括号");
				}
				so.pop();
				cout << "pop LP" << endl;
				new_val(so, v); 
			}
			else throw invalid_argument("缺少运算数"); 
		}
		else { 
			v = stod(exp.substr(p), &q);
			p += q;
			new_val(so, v);
		}
	}
	if (so.size() != 1 || so.top().t != VAL)
		throw invalid_argument("非法表达式");
	cout << so.top().v << endl;
	return 0;
}
```

## 第十章

### 10.3

```c++
//用accumulate求一个vector<int>中的元素之和
#include<iostream>
#include<fstream>
#include<vector>
#include<algorithm>
#include<numeric>
using namespace std;
int main(int argc, char *argv[]) {
	ifstream in(argv[1]);
	if (!in) {
		cout << "打开文件失败" << endl;
		exit(1);
	}
	vector<int> v;
	int val;
	while (in >> val) {
		v.push_back(val);
	}
	cout << accumulate(v.begin(), v.end(), 0) << endl;
	return 0;
}
```

### 10.15

```c++
//编写一个lambda，捕获它所在函数的int，并接受一个int参数。lambda应该返回捕获的int和int参数的和
#include<iostream>
using namespace std;
void add(int a) {
	auto sum = [a](int b) { return a + b; };
	cout << sum(1) << endl;
}
int main(void) {
	add(1);
	add(2);
	system("pause");
	return 0;
}
```

### 10.34

```c++
//使用反向迭代器逆序打印一个vector
#include<iostream>
#include<fstream>
#include<vector>
#include<algorithm>
using namespace std;
int main(int argc, char *argv[]) {
	if (argc != 2) {
		cout << "用法: execise.exe in_file" << endl;
		return -1;
	}
	ifstream in(argv[1]);
	if (!in) {
		cout << "打开输入文件失败" << endl;
		exit(1);
	}
	vector<int> v;
	int vi;
	while (in >> vi) {
		v.push_back(vi);
	}
	for (auto r_iter = v.crbegin(); r_iter != v.rend(); ++r_iter) {
		cout << *r_iter << " ";
	}
	cout << endl;
	//system("pause");
	return 0;
}
```

### 10.42

```c++
//使用list代替vector重新实现去除重复单词的程序
#include<iostream>
#include<fstream>
#include<list>
#include<string>
#include<algorithm>
using namespace std;
inline void output_words(list<string> &words) {
	for (auto iter = words.begin(); iter != words.end(); ++iter) {
		cout << *iter << endl;
	}
	cout << endl;
}
void elimDups(list<string> &words) {
	output_words(words);
	words.sort();
	output_words(words);
	words.unique();
	output_words(words);
}
int main(int argc, char *argv[]) {
	ifstream in(argv[1]);
	if (!in) {
		cout << "打开文件失败" << endl;
		exit(1);
	}
	list<string> words;
	string word;
	while (in >> word) {
		words.push_back(word);
	}
	elimDups(words);
	//system("pause");
	return 0;
}
```

## 第十一章

### 11.12

```c++
//编写程序，读入string和int序列，将每个string和int存入一个pair中，pair保存在一个vector中
#include<iostream>
#include<fstream>
#include<utility>
#include<vector>
#include<string>
#include<algorithm>
using namespace std;
int main(int argc, char *argv[]) {
	ifstream in(argv[1]);
	if (!in) {
		cout << "fail" << endl;
		exit(1);
	}
	vector<pair<string, int>> data;
	string s;
	int v;
	while (in >> s && in >> v) {
		data.push_back(pair<string, int>(s, v));
	}
	for (const auto &d : data) {
		cout << d.first << " " << d.second << endl;
	}
	system("pause");
	return 0;
}
```

### 11.17

####   set的迭代器是const的，因此只允许访问set中元素，而不能改变set。与map一样，set的关键字也是const，因此不能通过迭代器来改变set中元素的值

####   因此，前两个调用试图将vector中的元素复制到set中，是非法的。

####   而后两个调用将set中的元素复制到vector中，是合法的。

### 11.38

```c++
//用unordered_map重写单词技术程和单词转换程序
#include<iostream>
#include<vector>
#include<unordered_map>
#include<string>
#include<stdexcept>
#include<sstream>
#include<fstream>
using namespace std;
unordered_map<string, string> buildMap(ifstream &map_file) {
	unordered_map<string, string> trans_map;
	string key;
	string value;
	while (map_file >> key && getline(map_file, value)) {
		if (value.size() > 1) {
			trans_map[key] = value.substr(1);
		}
		else throw runtime_error("no rule for " + key);
	}
	return trans_map;
}
const string & transform(const string &s, const unordered_map<string, string> &m) {
	auto map_it = m.find(s);
	if (map_it != m.cend())
		return map_it->second;
	else return s;
}
void word_transform(ifstream &map_file, ifstream &input) {
	auto trans_map = buildMap(map_file);
	string text;
	while (getline(input, text)) {
		istringstream stream(text);
		string word;
		bool firstword = true;
		while (stream >> word) {
			if (firstword)
				firstword = false;
			else cout << " ";
			cout << transform(word, trans_map);
		}
		cout << endl;
	}
}
int main(int argc, char **argv) {
	if (argc != 3)
		throw runtime_error("wrong number of arguments");
	ifstream map_file(argv[1]);
	if (!map_file)
		throw runtime_error("no transformation file");
	ifstream input(argv[2]);
	if (!input) 
		throw runtime_error("no input file");
	word_transform(map_file, input);
	system("pause");
	return 0;
}
```

## 第十三章

### 13.12

#### （1）函数结束时，局部变量 `item1` 的生命期结束，被销毁，`Sales_data` 的析构函数被调用

#### （2）`item2`在函数结束时被销毁，`Sales_data` 的析构函数被调用。

#### （3）函数结束时，参数 `accum` 的生命周期结束，被销毁，`Sales_data` 的析构函数被调用

### 13.18

```c++
//定义一个Employee类，它包含雇员的姓名和唯一的雇员证号。为这个类定义默认构造函数，以及接受一个表示雇员姓名的srting的构造函数。每个构造函数应该通过递增一个static数据成员来生成一个唯一的证号
#include<iostream>
#include<string>
using namespace std;
class Employee {
private:
	static int sn;
public:
	Employee() { mysn = sn++; }
	Employee(const string &s) { name = s; mysn = sn++; }
	//Employee(Employee &e) { name = e.name; mysn = sn++; }
	//Employee& operator=(Employee &rhs) { name = rhs.name; return *this; }
	const string &get_name() { return name; }
	int get_mysn() { return mysn; }
private:
	string name;
	int mysn;
};
int Employee::sn = 0;
void f(Employee &s) {
	cout << s.get_name() << ":" << s.get_mysn() << endl;
}
int main(void) {
	Employee a("李"), b = a, c;
	c = b;
	f(a);
	f(b);
	f(c);
	//system("pause");
	return 0;
}
```

### 13.46

#### （1）`r1` 必须是右值引用，因为 `f` 返回非引用类型函数，返回值是一个右值

#### （2）`r2` 必须是左值引用，因为下标运算返回的是左值。

#### （3）`r3` 只能是左值引用，因为 `r1` 是一个变量，而变量是左值

#### （4）`r4` 只能是右值引用，因为 `vi[0] * f()` 是一个算术运算表达式，返回右值。

### 13.49

```c++
//为你的StrVec, String和Message类添加一个移动构造函数和一个移动赋值运算符
#include<iostream>
#include<string>
using namespace std;
String &string::operator=(String &&rhs) noexcept {
	if (this != &rhs) {
		if (p)
			a.deallocate(p, sz);
		p = rhs.p;
		sz = rhs.sz;
		rhs.p = 0;
		rhs.sz = 0;
	}
	return *this;
}
int main(void) {

}
```

### 13.58

```c++
//编写新版本的Foo类，其sorted函数中有打印语句，测试这个类。
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;
class Foo {
public:
	Foo sorted() && ;
	Foo sorted() const &;
private:
	vector<int> data;
};
Foo Foo::sorted() && {
	cout << "右值引用版本" << endl;
	sort(data.begin(), data.end());
	return *this;
}
Foo Foo::sorted() const & {
	cout << "左值引用版本" << endl;
	Foo ret(*this);
	return ret.sorted();
}
int main(void) {
	Foo f;
	f.sorted();
	system("pause");
	return 0;
}
```

## 第十四章

### 14.3

#### （1）`"cobble" == "store"` 应用了C++语言内置版本的 `==` ，比较两个指针。

#### （2）`svec1[0] == svec2[0]` 应用了 `string` 版本的重载 `==` 

#### （3）`svec1 == svec2` 应用了 `vector` 版本的重载 `==`

#### （4）`svec1[0] == "stone"` 应用了 `string` 版本的重载 `==` ，字符串字面常量被转换为 `string`

### 14.20

```c++
//为你的Sales_data类定义加法和复合赋值运算符
#include<iostream>
#include<algorithm>
using namespace std;
class Sales_data {
	friend Sales_data operator+ (const Sales_data &lhs, const Sales_data &rhs);
public:
	Sales_data& operator+= (const Sales_data &rhs);
};
Sales_data operator+ (const Sales_data &lhs, const Sales_data &rhs) {
	Sales_data sum = lhs;
	sum += rhs;
	return sum;
}
Sales_data& Sales_data::operator+= (const Sales_data &rhs) {
	uints_sold += rhs.units_sold;
	revenue += rhs.revenue;
	return *this;
}
int main(void) {

}
```

### 14.38

```c++
//编写一个类令其检查某个给定的string对象的长度是否与一个阈值相等。使用该对象编写程序，统计并报告在输入的文件中长度为1的单词有多少个，长度为2的单词有多少个……长度为10的单词又有多少个
#include<iostream>
#include<vector>
#include<string>
#include<algorithm>
using namespace std;
class StrLenIs {
public:
	StrLenIs(int len) :len(len) {}
	bool operator() (const string &str) {
		return str.length() == len;
	}
private:
	int len;
};
void readStr(istream &is, vector<string> &vec) {
	string word;
	while (is >> word) {
		vec.push_back(word);
	}
}
int main(void) {
	vector<string> vec;
	readStr(cin, vec);
	const int minLen = 1;
	const int maxLen = 10;
	for (int i = minLen; i <= maxLen; ++i) {
		StrLenIs slenIs(i);
		cout << "len :" << i << ", cnt: " << count_if(vec.begin(), vec.end(), slenIs);
	}
	//system("pause");
	return 0;
}
```

### 14.52

####   对于`ld = si + ld` ， 由于`LongDouble` 不能转换为`SmallInt` ，因此`SmallInt` 的成员`operator+`  和`friend operator+`  都不可行。

####   由于`SmallInt` 不能转换为，`LongDouble` ，`LongDouble` 的成员`operator+` 和非成员`operator+` 也都不可行。

####   由于`SmallInt` 可以转换为`int`， `LongDouble`可以转换为`float`和`double`，所以内置的`operator+（int， float）`和`operator+（int， double）`都可行，会产生二义性

####   对于`ld = ld + si`， 类似加上一个加法表达式，由于`SmallInt`不能转换为`double`，`LongDouble`也不能转换为`SmallInt`，因此`SmallInt`的成员`operator+`和两个非成员`operator+`都不匹配

####   `LongDouble`的`operator+`可行，且为精确匹配。

####   `SmallInt`可以转换为`int`，`LongDouble`可以转换为`float`和`double`，因此内置的`operator+（float， int）`都可行。但是它们都需要类型转换，因此`LongDouble`的成员`operator+`优先匹配

## 第十五章

### 15.12

#### 有必要。

#### `override`：在C++11新标准中可以使用`override`关键字来说明派生类中的虚函数。这么做可以更明确的告诉编译器我们想要覆盖掉已存在的虚函数。如果定义了一个函数与基类中的名字相同但是形参列表不同，在不是用`override`关键字的时候这种函数定义是合法的，在使用了`override`关键字之后这种行为是非法的，编译会提示出错。

#### `final`：如果我们将某个函数定义成`final`，则不允许后续的派生类来覆盖这个函数否则会报错。

### 15.16

```c++
//继承的函数如下
using namespace std;
class Limited_quote: public Disc_quote {
public:
	Limited_quote(const string &book = "", double sales_price = 0.0, size_t qty = 0, double disc_rate = 0.0) :
		Disc_quote(book, sales_price, qty, disc_rate) { }
	double nei_price(size_t cnt) const override {
		if (cnt <= quantity)
			return cnt * (1 - discount) * price;
		else return quantity * (1 - discount) * price + (cnt - quantity) * price;
	}
};
int main(void) {

}
```

### 15.30

```c++
//编写的Basket类如下
using namespace std;
class Basket {
public:
	void add_item(const std::shared_ptr<Quote> &sale) {
		items.insert(sale);
	}
	double total_receipt(ostream&) const;
private:
	static bool compare(const share_ptr<Quote> &lhs, const share_ptr<Quote> &rhs) {
		return lhs->isbn() < rhs->isbn();
	}
	multiset<shared_ptr<Quote>, decltype(compare)* > item{ compare };
};
double Basket::total_receipt(ostream &os) const {
	double sum = 0.0;
	for (auto iter = items.cbegin(); iter != items.cend(); iter = items.upper_bound(*iter)) {
		sum += price_total(os, **iter, items.count(*iter));
	}
	os << "Total Sale:" << sum << endl;
	return sum;
}
int main(void) {

}
```

