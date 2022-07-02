## Код для решения задачи протранственно-неоднородной коагуляции в одномерном пространстве и с источником мономеров.

Источник мономеров на левой границе позволяет нам говорить о стационарном решении

* Параметры:
`h` - шаг по размерам частиц
`dt` - шаг по времени
`J` - величина источника
`TIME` - общее количество итераций
`MOD` - периодичность, с которой расчеты записываются в файлик
`max_particle_size` - общее количество разбиений по размерам частиц
`max_x` - общее количество разбиений по пространственной координате
`dx` - шаг по пространству
`vel_coefs` - массив коэффициентов переноса для разных размеров частиц
`dif_coefs` - массив коэффиципнтов диффузии для разных размеров частиц
`initial_layer` - массив с начальным условием (по дефолту нулевое)

* Источник мономеров задается в виде граничного условия в файле main.cpp (см строку **276** - rhs). Также см. строки **303-311** (там указаны коэффициенты первой и последней строки трехдиагональной матрицы, можно сделать условие Дирихле, Неймана и т.д. Видим, что слева [0+m*max_x] условие Неймана, а справа [(max_x-1)+m*max_x] условие Дирихле.

* Как записываются данные в файл. Каждые `MOD` итераций по времени происходит запись. Записываются `max_particle_size` значений (строка **229** в файле main.cpp), затем переходим на новую строку (строка **231** в файле main.cpp). В итоге получается матрица размером `max_particle_size` на `TIME/MOD * max_x`. Эти данные можно считать в питоне и сделать __.reshape__ (см. файл draw_article.ipynb).

* В коде есть примеры 4 запусков:
1. res_diff - случай с только с константной диффузией без переноса и константным ядром коагуляции
2. res_adv - случай с констнатными коэффициентами диффузии и переноса и константным ядром коагуляции
3. res_full - случай с переменными коэффциентами диффузии и переноса и простым ядром со степенью однородности $$\frac{4}{3}$$.
4. res_ballistic - случай с переменными коэффциентами диффузии и переноса и баллистическим ядром

Чтобы сделать нужный запуск закомментите ненужное и разкомментите нужное в файлах main.cpp (строки **65**-**114**, **149**-**165**, **279**) и в файле wrappers.cpp (строки **9**-**24**).

* Оператор коагуляции можно вычислять напрямую (**255**-**265**) либо через преобразование Фурье (**234**-**253**). Файл main.cpp

* в библиотеке есть зависимости от blas, lapack и fftw3. Но если запускать без малорангового разложения ядра коагуляции можно от этих зависимостей избавиться. Но вам нужно будет самим убирать ненужные #include'ы, убрать вызовы crossed_kernel, fftw... и поправить мейкфайл. Если нужна будет помощь то пишите. Если в лом, то можно просто установить себе эти три библиотеки. На жоресе blas и lapack есть по дефолту, а fftw3 можно подключить через `module load`. (на Жоресе только посмотрел комплится ли файл, сам запуск не делал, если не получится, пишите)

* Аргументы командной строки. Например, если запускать код командой solver.exe output.txt 0, то появится файл output.txt куда будут записываться данные. Если запускать командой solver.exe output.txt 5 то код предполагает что файл output.txt уже существует и там уже успелось записаться 6 итераций по времени. Тогда код первые 5 итераций пропускает и считывает шестую итерацию как начальное условие и продолжает расчеты.
