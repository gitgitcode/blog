## 组合 聚合 被引用的对象实例成为对象的一部分
###策略模式 Strategy 。策略模式 适用于将一组算法一如到一个单独的类型中。


		abstract class lesson{

			private $duration;
			private $constStrategy;
		
			function __construct($duration, CostStrategy $strategy ){//类型检查 必须是CostStrategy 类
				$this->duration = $duration;
				$this->constStrategy = $strategy;
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
		//
