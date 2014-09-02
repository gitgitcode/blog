## 组合 聚合 被引用的对象实例成为对象的一部分
###策略模式 Strategy 。策略模式 适用于将一组算法一如到一个单独的类型中。


		abstract class lesson{

			private $duration;
			private $constStrategy;
		
			function __construct($duration, CostStrategy $strategy ){//类型检查 必须是CostStrategy 类
				$this->duration = $duration;
				$this->constStrategy = $strategy;//将算法提取为 CostStrategy 类 ，放置在共同接口中
			}
			function cost(){
				return $this->constStrategy->cost($this);
			}
			function chargeType(){
				return $this->constStrategy->chargeType();
			}
			function getDuration(){
				return $this->duration;
			}
		}

		class lecture extends lesson {
		
		}
		class seminar extends lesson{
		
		}
>
		//将算法提取为 CostStrategy 类 ，放置在共同接口中每个算法只实现一次
		abstract class CostStrategy {
			abstract function cost( lesson $lesson );
			abstract function chargeType();
		}
		
		class timedcoststrategy extends CostStrategy{
			function cost(lesson $lesson){
				return ($lesson->getDuration() * 5 );
			}
			function chargeType(){
				return "hourly rate ";
			}
		}
		class fixedcoststrategy extends CostStrategy{
			function cost(lesson $lesson){
				return 30;
			}
			function chargeType(){
				return "fixed rate ";
			}
		}
>
***
		//动态组合重组对象 相对于 继承要好很多
		
		$lesson[] = new seminar(7,new timedcoststrategy());
		$lesson[] = new lecture(9,new fixedcoststrategy());
		 
		foreach ($lesson as $key => $value) {
			 print "lesson charge {$value->cost()} ";
			 print "lcharge type: {$value->chargeType()} <br/>";
		}


		//lesson charge 35lcharge type: hourly rate 
		//lesson charge 30lcharge type: fixed rate 
***
>
	class RegistrationMgr{
		function register(lesson $lesson){
			$notifier = Notifier::getNotifier();
			$notifier->inform("new lesson : cost ({$lesson->cost()})");
		}
	}

>

	abstract class Notifier{//抽象类
		static function getNotifier(){//静态方法，static 指向当前调用本身
			if(rand(1,2)==1){
				return new MailNotifier();
			}else{
				return new TextNotifier();
			}
		}
		//$notifier = Notifier::getNotifier(); 外部调用
		
		//$notifier->inform("new lesson : cost ({$lesson->cost()})"); 通过 RegistrationMgr 实现
		abstract function inform($message);
	}
	
	
	
	class MailNotifier extends Notifier{
		function inform($message){
			print "MAIL notifiaction :{$message}";
		}
	}
	class TextNotifier extends Notifier{
		function inform($message){
			print "Text notifiaction :{$message}";
		}
	}
	
	$msg = new RegistrationMgr();
	$msg ->register($lesson[1]);
	//MAIL notifiaction :new lesson : cost (30)
