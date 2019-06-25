# What is the most efficient way to loop through dataframes with pandas? [duplicate]
https://stackoverflow.com/questions/7837722/what-is-the-most-efficient-way-to-loop-through-dataframes-with-pandas
~~~
t = pd.DataFrame({'a': range(0, 10000), 'b': range(10000, 20000)})
B = []
C = []
A = time.time()
for i,r in t.iterrows():
    C.append((r['a'], r['b']))
B.append(time.time()-A)

C = []
A = time.time()
for ir in t.itertuples():
    C.append((ir[1], ir[2]))    
B.append(time.time()-A)

C = []
A = time.time()
for r in zip(t['a'], t['b']):
    C.append((r[0], r[1]))
B.append(time.time()-A)

print B
Result:

[0.5639059543609619, 0.017839908599853516, 0.005645036697387695]
~~~
