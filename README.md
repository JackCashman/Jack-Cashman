# Jack-Cashman

const input = document.querySelector('#search-input');
const suggestionBox = document.querySelector('#suggestion-box');

// 监听输入框输入
input.addEventListener('input', async () => {
  const keyword = input.value.trim();

  if (keyword === '') {
    suggestionBox.innerHTML = '';
    return;
  }

  // 发送Ajax请求
  const res = await fetch(`/suggestion?keyword=${keyword}`);
  const suggestions = await res.json();

  // 渲染搜索建议列表
  suggestionBox.innerHTML = '';
  suggestions.forEach(suggestion => {
    const div = document.createElement('div');
    div.textContent = suggestion;
    suggestionBox.appendChild(div);
  });
});
</script>

@app.route('/suggestion')
def suggestion():
    keyword = request.args.get('keyword')
    suggestions = get_suggestions(keyword)
    return jsonify(suggestions)

def get_suggestions(keyword):
    # 在数据库中查找与输入词相关的建议
    suggestions = db.execute(
        'SELECT keyword FROM search_history WHERE keyword LIKE ? LIMIT 10',
        (f'%{keyword}%',)
    ).fetchall()

    # 将结果转换为字符串列表
    suggestions = [row['keyword'] for row in suggestions]

    return suggestions

<div id="hot-search">
  <div class="title">热门搜索</div>
  <ul>
    {% for keyword in hot_keywords %}
    <li><a href="/search?q={{ keyword }}">{{ keyword }}</a></li>
    {% endfor %}
  </ul>
</div>

def get_hot_keywords():
    # 在数据库中查找最近一段时间内搜索量最大的关键词
    hot_keywords = db.execute(
        'SELECT keyword, COUNT(*) AS cnt FROM search_history WHERE timestamp > ? GROUP BY keyword ORDER BY cnt DESC LIMIT 10',
        (time.time() - 7 * 86400,)
    ).fetchall()

    # 将结果转换为关键词列表
    hot_keywords = [row['keyword'] for row in hot_keywords]

    return hot_keywords

@app.route('/history')
def history():
    # 在数据库中查找最近一段时间内搜索历史记录
    history = db.execute(
        'SELECT keyword, timestamp FROM search_history WHERE timestamp > ? ORDER BY timestamp DESC LIMIT 10',
        (time.time() - 7 * 86400,)
    ).fetchall

