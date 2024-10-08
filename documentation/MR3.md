# Что такое $MR^3$?

## Некоторые условности

Перейдем к основной идее. Будем считать что на вход подается трехдиагональная матрица $T$

$$T = diag(c_1, .., c_n) + diag_{\pm1}(e_1, ..., e_n)$$

где $diag_k$ - k-я наддиагональ  и (-k)-я поддиагональ соответственно.Вне главной диагонали находятся ненулевые элементы. Эти значения подчинаются следующему соотношению:

$$e_i > ||T||n \epsilon_\diamond,$$

где $\epsilon_\diamond$ - машинный ноль. 

Будем считать, что матрица $T$ определяет собственное значение $\lambda$ и собственный вектор $v$, для которых мы хотим повысить относительную погрешность (позже уточним что это). Соответственно мы можем вычислить приближенное значение $\bar\lambda$

$$|\bar\lambda - \lambda| = O(|\lambda| n \epsilon_\diamond)$$ 

или даже

 $$|\bar\lambda - \lambda| =O(|\lambda|\epsilon_\diamond).$$


$MR^3$ позволяет вычислить приближение $\bar v$ для собственного вектора $v$ с достаточно малой по норме невязкой относительно собственного значения, т. е.

$$||(T-\bar\lambda)\bar v|| \leq O(|\lambda|n \epsilon_\diamond).$$

## Алгоритм
__INPUT__: трехдиагональная матрица $T \in R^{n \times n}$ и множество индексов $I_0 \subseteq \{1, .., n\}$.

__OUTPUT__: приближенные пары собственных значений и соответствующих им векторов $(\bar \lambda_i, \bar v_i)$

__PARAMS__: $gaptol$ (можно считать расстоянием от собственного значения до остального спектра, может принимать значение, например, 0.001)

1. Препроцессинг, но сейчас для простоты будем считать, что $T$ - хорошая матрица.
2. $S = \{(T, I_0, \bar \tau = 0)\}$
3. while $S \neq \emptyset$ {
4. убираем один узел $\{(T, I_0, \bar \tau = 0)\}$ из $S$
5. аппроксимируем значения $[\lambda^{loc}_i], i \in I$ матрицы $T$ таким образом, чтобы мы могли классифицировать их по кластерам или по одиночке согласно $gaptol$. То есть мы делаем разбиение $I=I_1 \cup I_2 \cup...\cup I_m $
6. for r = 1 to m {
7. if $I_r = \{i\}$ {        //изолированное значение
8. уточняем собственное значение $[\lambda^{loc}_i]$ и используем его для вычисления $\bar v_i$. При необходимости итерируемся до тех пор пока невязка вектора $\bar v_i$ не станет достаточно малой, используя *Rayleigh quotient iteration (RQI)*
9. $\bar \lambda_i := \lambda^{loc}_i + \bar \tau$
10. } else {            //кластер
11. уточняем собственное значение на границе кластера, если хотим более точный выбор смещения. Выбираем подходящее смещение $\tau$ рядом с кластером и считаем представление $T^+=T-\tau$
12. добавляем новый узел $(T^+, I_r, \bar \tau + \tau)$ в *S*

}
}
}
