========
WebQuery
========

WebQuery is a Pythonic way to interact with API's using SQL. In other words, *Python + SQLite ≈ YQL*.

Dependencies:
 - yaml
 - lxml
 - apsw

Interactive shell::

 ./webquery.py *.yml

Using a pipe::

 echo "SELECT * FROM delicious_feeds_popular WHERE query='test' ORDER BY url DESC; SELECT url,title FROM microsoft_bing_web WHERE query='test' LIMIT 5;" | ./webquery.py *.yml

Limiting requests (useful for sorting)::

 echo "SELECT webquery('microsoft_bing_web','truncate',50); SELECT title,url FROM microsoft_bing_web WHERE query='sushi' ORDER BY title;" | ./webquery.py *.yml

In Python::

 import apsw, webquery
 db = apsw.Connection(':memory:')
 webquery.attach(db)
 cur = db.cursor()
 cur.execute('CREATE VIRTUAL TABLE microsoft_bing_web USING webquery(microsoft.bing.web.yml);')
 cur.execute('SELECT webquery("microsoft_bing_web","truncate",50);')
 for title,url in cur.execute('SELECT title,url FROM microsoft_bing_web WHERE query="sushi" ORDER BY title;'):
     print title,'>',url

nb:
 - The ORDER BY clause does not map to server-side sorting; it sorts the results that are returned by the server.
 - Request truncation (by the `truncate` variable) always takes precedence over the LIMIT clause, preventing unbounded requests while performing a sorted query. It does not guarantee that the result set be smaller than the set value, so the LIMIT clause is still useful.
