1.准备好虚拟环境和安装相关配置
2.创建flask项目
3.创建settings并配settings文件
    class Config:
        ENV = 'development'
        DEBUG = True
        # mysql+pymysql://user:password@hostip:port/databasename
        SQLALCHEMY_DATABASE_URI = 'mysql+pymysql://root:mysql@127.0.0.1:3306/flaskday04'
        SQLALCHEMY_TRACK_MODIFICATIONS = False
        SQLALCHEMY_ECHO = True
    class DevelopmentConfig(Config):
        ENV = 'development'
    class ProductionConfig(Config):
        ENV = 'production'
        DEBUG = False
4.创建Python Package：apps和exts包
  在apps下的__init__.py中：
  创建create_app()方法，如下：
  def create_app():
    app = Flask(__name__, template_folder='../templates', static_folder='../static')
    app.config.from_object(settings.DevelopmentConfig)

    return app

  在app.py文件下：
  app = create_app()
  manager = Manager(app=app)

  if __name__ == '__main__':
      manager.run()

  在exts下__init__.py文件中：
  from flask_sqlalchemy import SQLAlchemy

  db = SQLAlchemy()

  在回到apps/__init__.py中初始化db配置db
  # 初始化配置db
  db.init_app(app=app)

  在回到app.py中配置（安装flask-migrate==2.7.0）
  migrate = Migrate(app=app, db=db)
  manager.add_command('db', MigrateCommand)

5.在apps/创建Python Package:(user)
  在apps/user/创建view.py、models.py
  在models.py文件下：
  class User(db.Model):
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    username = db.Column(db.String(15), nullable=False)
    password = db.Column(db.String(12), nullable=False)
    phone = db.Column(db.String(11), unique=True)
    rdatetime = db.Column(db.DateTime, default=datetime.now)

    def __init__(self):
        return self.username

    在flaskProject05/app.py中导入User类
    from apps.user.models import User

    在终端操作命令：
    python app.py db init      初始化产生migrations文件夹
    python app.py db migrate   生成版本文件 509986db4cba_.py
    python app.py db upgrade   同步（使版本文件中的创建的数据库同步到mysql数据库中）

6.在flaskProject05/apps/user/view.py中
  注册路由
  use_bp = Blueprint('user', __name__)

  @use_bp.route('/register', methods=['GET', 'POST'])
  def register():
        if request.method == 'POST':
            pass
        return render_template('register.html')

  在回到/user/__init_.py中注册蓝图
  # 注册蓝图
  app.register_blueprint(use_bp)

register.html 注册页面
<form action="{{ url_for('user.register') }}" method="POST">
    <p><input type="text" name="username" placeholder="用户名"></p>
    <p><input type="password" name="password" placeholder="密码"></p>
    <p><input type="password" name="repassword" placeholder="确认密码"></p>
    <p><input type="text" name="phone" placeholder="手机号"></p>
    <p><input type="submit" value="用户注册"></p>
</form>