+++
categories = ["memo", "python", "SQLAlchemy", "Flask", "web"]
date = "2015-05-20T23:49:04+09:00"
description = "SQLAlchemyを利用したupdateについてのメモ"
jtitle = "SQLAlchemyでのupdate"
keywords = ["memo", "python", "SQLAlchemy", "Flask"]
title = "sqlalchemy_update_20150520"

+++
# 概要
ノイズのある```dictionary```データからSQLAlchemyを使って簡単にupdateしたい。
フォームからデータを受け取るとModelに必要のないデータも含まれてしまうことがある。
そういったものをいちいち指定せずによしなにupdateを行えるようにする。

# 通常のupdateの方法
通常のupdateは次のような感じ。

```python
stmt = Users.update().\
            where(Users.id==5).\
            values(name='user #5')
```

もしくは

```python
user = Users.query.filter_by(id=5).first()
user.name="hoge"
db.session.commit()
```
# 今回の方法
`setattr(obj, key, value)`を使っていく。

```python
class Users(db.Model):
    id = db.Column(db.Integer, primarykey=True)
    name = db.Column(db.String(100), nullable=False, unique=True)
    password = db.Column(db.String(100), nullable=False)

    def __init__(self, id, name, password):
        self.id = user_id
        self.name = name
        self.password = password

    def update_dict(dict):
        for name, value in dict.items():
            if name in self.__dict__:
                setattr(self, name, value)


# 更新操作
dict = {"name"; hoge, "password":fuga}
user=Users.query.filter_by(id=5).first()
user.update_form(dict)
db.session.commit()
```

こんな感じの`update_dict`をモデルに実装すると、
`dictionary`を無造作に入れても、定義されている値のみが変更され、
データの更新が行われるので楽。
フォームをモデルに合うように作るだけで、データの追加更新を簡単に実装できるようになる。

