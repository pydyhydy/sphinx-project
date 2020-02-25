====================
章のタイトル
====================

:項目１: フィールドリストの内容１
:項目２: 内容２（これはフィールドリストです）

セクションタイトル１
==========================

セクション１の最初のパラグラフ。そうです。
改行は無視されます。

必要なら別のパラグラフにする必要があります。

1. 番号付きのリスト
2. これが２番め

#. 自動番号にはこっちなのかな？
#. まあやってみましょう。

番号なしの箇条書きは、

* これは普通
* これも普通

 + 入れ子の箇条書き
 + これも確認したい


次のセクション
===============

こんな感じで追加していけばいいのでしょうかね。

いやいや、そういうことなのかな？:term:`最初のキーワード`

.. glossary::

 最初のキーワード
   これは最初のキーワードの定義です。



単語にいくつかの意味をもたせることができます。

- **強調**
- *斜体*
- ``リテラル``
- `リンク文字列A`_
- `リンク文字列B <http://docs.sphinx-users.jp>`__
- :doc:`index` 対象ファイルへのリンク。章タイトルに置き換わります。

.. _リンク文字列A: https://www.google.com


pythonのコードブロック
=========================

プログラムを載せてみましょう。

.. code-block:: python

    from flask import Flask, render_template, request, session, redirect, jsonify


    app = Flask(__name__)
    app.secret_key = b'random string...'


    member_data = {}
    message_data = []

    @app.route('/', methods=['GET'])
    def index():
        return render_template('index.html', \
            title='Index', \
            message='※Vue.js',
            data="['one', 'two', 'three']")

    @app.route('/ajax/<id>', methods=['GET'])
    def ajax(id):
        data = {
            1:{'id':1, 'name':'Taro', 'mail':'taro@yamada'},
            2:{'id':2, 'name':'Hanako', 'mail':'hanako@flower'},
            3:{'id':3, 'name':'Sachiko', 'mail':'sachico@happy'}
        }
        n = int(id)
        if n < 1:
            n = 1
        if n > len(data):
            n = 3
        return jsonify(data[n]);

    # login page access
    @app.route('/login', methods=['GET'])
    def login():
        return render_template('login.html', \
            title='Login', \
            err=False, \
            message='IDとパスワードを入力：', \
            id='' )


    # login form sended.
    @app.route('/login', methods=['POST'])
    def login_post():
        global member_data
        id = request.form.get('id')
        pswd = request.form.get('pass')
        if id in member_data:
            if pswd == member_data[id]:
                session['login'] = True
            else:
                session['login'] = False
        else:
            member_data[id] = pswd
            session['login'] = True
        session['id'] = id
        if session['login']:
            return redirect('/')
        else:
            return render_template('login.html', \
                title='Login', \
                err=False, \
                message='パスワードが違います', \
                id=id )


    # logout
    @app.route('/logout', methods=['GET'])
    def logout():
        session.pop('id', None)
        session.pop('login')
        return redirect('/login')


    if __name__ == '__main__':
        app.debug = True
        app.run(host='localhost')
