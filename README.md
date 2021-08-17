# kaggle_a-b_test_01
https://www.kaggle.com/zhangluyuan/ab-testing?select=ab_data.csv

# Тема: А/В тестирование
Двум группам людей показывали старую и новую версию сайта и фиксировали нажимали ли они на рекламу.</br>
Необходимо выяснить - изменилась ли конверсия на новой странице.</br>
Во входном датасете мною были обнаружены ошибки- неверно посталены группы</br>
#### перемещаем тех, кто видел старую версию в контрольную группу и наоборот 
```python
df.loc[(df.group == 'control') & (df.landing_page == 'new_page'), 'group'] = 'treatment' 
df.loc[(df.group == 'treatment') & (df.landing_page == 'old_page'), 'group'] = 'control'
```
и дубликаты по id пользователя.
```python
df_cleaned = df.drop_duplicates (subset=['user_id'], keep = "first")
```
После "очистки" данных использовал критерий хи-квадрат для проверки гипотезы об отсутствии существенного различия конверсий.
#### Разделим группы на два отдельных фрейма данных
```python
a = df[df['group'] == 'control'] 
b = df[df['group'] == 'treatment']
```
#### получаем 4 значения: группа "контрольная" - кликнули и не кликнули, то же самое и для другой группы
```python
a_click = a.converted.sum() 
a_noclick = a.converted.size - a.converted.sum() 
b_click = b.converted.sum() 
b_noclick = b.converted.size - b.converted.sum()
```
#### Создаем массив со значениями
```python
T = np.array ([[a_click, a_noclick], [b_click, b_noclick]])
scipy.stats.chi2_contingency(T, correction = False)[1]
```
### Значение p составило 17%. Уровень значимости 5%. Мы можем сделать вывод, что значение p больше, чем уровень значимости. Нулевая гипотеза подтвердилась - конверсия между старой и новой веб-страницей не имеет существенного значения.
