# TEST 02

Icon here :material-check:

Another icon: :pushpin:

Test smarty:

- Quotes: 'I am in quotes'
- Dashes: double -- dashes


Lorem ipsum[^1] dolor sit amet, consectetur adipiscing elit.[^2]

[^1]: Lorem ipsum dolor sit amet, consectetur adipiscing elit.


[^2]:
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
    nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.

- [x] Lorem ipsum dolor sit amet, consectetur adipiscing elit
- [ ] Vestibulum convallis sit amet nisi a tincidunt
    * [x] In hac habitasse platea dictumst
    * [x] In scelerisque nibh non dolor mollis congue sed et metus
    * [ ] Praesent sed risus massa
- [ ] Aenean pretium efficitur erat, donec pharetra, ligula non scelerisque


## Admonitions

!!! note "I am a note"

    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
    nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.

??? note "Collapsible note"

    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
    nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.

Aaaa aaaaaa aaa aa aa aaaaa aa aa aa a aa. Aaaa aaaaaa aaa aa aa aaaaa aa aa aa a aa. Aaaa aaaaaa aaa aa aa aaaaa aa aa aa a aa. Aaaa aaaaaa aaa aa aa aaaaa aa aa aa a aa. Aaaa aaaaaa aaa aa aa aaaaa aa aa aa a aa. Aaaa aaaaaa aaa aa aa aaaaa aa aa aa a aa. Aaaa aaaaaa aaa aa aa aaaaa aa aa aa a aa. 

!!! info inline end "I am a side note on the right of the following element"

    Lorem ipsum dolor sit amet, consectetur
    adipiscing elit. Nulla et euismod nulla.
    Curabitur feugiat, tortor non consequat
    finibus, justo purus auctor massa, nec
    semper lorem quam in massa.

Bbbbb bbb b b bbbbbbb bbbbbbb bbb b bbbbbb bbbbbb b bbb b bbb b bbbb bb b b bbbbb b b bbbbbbb. Bbbbb bbb b b bbbbbbb bbbbbbb bbb b bbbbbb bbbbbb b bbb b bbb b bbbb bb b b bbbbb b b bbbbbbb. Bbbbb bbb b b bbbbbbb bbbbbbb bbb b bbbbbb bbbbbb b bbb b bbb b bbbb bb b b bbbbb b b bbbbbbb. Bbbbb bbb b b bbbbbbb bbbbbbb bbb b bbbbbb bbbbbb b bbb b bbb b bbbb bb b b bbbbb b b bbbbbbb. Bbbbb bbb b b bbbbbbb bbbbbbb bbb b bbbbbb bbbbbb b bbb b bbb b bbbb bb b b bbbbb b b bbbbbbb. 

!!! example

    === "Unordered List"

        ``` markdown
        * Sed sagittis eleifend rutrum
        * Donec vitae suscipit est
        * Nulla tempor lobortis orci
        ```

    === "Ordered List"

        ``` markdown
        1. Sed sagittis eleifend rutrum
        2. Donec vitae suscipit est
        3. Nulla tempor lobortis orci
        ```



=== "C"

    ``` c
    #include <stdio.h>

    int main(void) {
      printf("Hello world!\n");
      return 0;
    }
    ```

=== "C++"

    ``` c++
    #include <iostream>

    int main(void) {
      std::cout << "Hello world!" << std::endl;
      return 0;
    }
    ```


These are all the options:


!!! note "I am note"

    This is a note.

!!! abstract "I am abstract"

    This is a abstract.

!!! info "I am info"

    This is a info.

!!! tip "I am tip"

    This is a tip.

!!! success "I am success"

    This is a success.

!!! question "I am question"

    This is a question.

!!! warning "I am warning"

    This is a warning.

!!! failure "I am failure"

    This is a failure.

!!! danger "I am danger"

    This is a danger.

!!! bug "I am bug"

    This is a bug.

!!! example "I am example"

    This is a example.

!!! quote "I am quote"

    This is a quote.

``` py title="bubble_sort.py"
def bubble_sort(items):
    for i in range(len(items)):
        for j in range(len(items) - 1 - i):
            if items[j] > items[j + 1]:
                items[j], items[j + 1] = items[j + 1], items[j]
```
