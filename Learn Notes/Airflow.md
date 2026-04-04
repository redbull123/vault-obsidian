# Apache Airflow - Introduction
- Workflow are many tasks for to do something.
Airflow where you can do the following actions:
- Creation
- Scheduling
- Monitoring
	DAG (Directed Acycllic Graph)
 Running a simple Airflow task
 > airflow tasks test <dag_id> <task_id> [execution_date]

default_params is optional, but it is the help.
## Sensor
- Email
- file
## Aditional Operator
### op_kwargs example
	def sleep(length_of_time):
		time.sleep(length_of_time)
	sleep_task = PythonOperator(
		task_if='sleep',
		python_callable=sleep,
		op_kwargs={'length_of_time':5})

![[Cron Syntax.png]]

## Debugging
	aiflow dags list-import-errors

## Templated BashOperator example
	 templated_command="""
		 echo "Reading {{ params.filename }}"
	 """
	 t1 = BashOperator(task_id='template_task',
			 bash_command=templated_command,
			 params={'filename':'file1.txt'},
			 dag=example_dag)
## More Advanced template
	templated_command="""
	{% for filename in params.filenames %}
		echo "Reading {{ filename }}"
	{% endfor %}
	"""
	t1 = BashOperator(task_id='template_task',
		bash_command=templated_command,
		params={'filenames':['file1.txt','file2.txt']},
		dag=example_dag)
## Variables
	Execution Date: {{ ds }}
	Execution Date, no dashes: {{ ds_nodash }}
	Previous Execution date: {{ prev_ds }}
	Prev Execution date, no dashes: {{ prev_ds_nodash }}
	DAG object: {{ dag }}
	Airflow config object: {{ conf }}
## Macros
	{{ macros.datetime }} = datetime.datetime
	{{ macros.timedelta }} = timedelta
	{{ macros.uuid }} = python uuid object
	{{ macros.ds_add('2020-04-15', 5)}}
# Branching
 - Provides conditional logic
 - from airflow.operators.python import BranchPythonOperator
Example:
	def branch_test(** kwargs):
		if int(kwargs['ds_nodash'])% 2 == 0:
			return 'event_day_task'
		else:
			return 'odd_day_task'
	branch_task = BranchPythonOperator(task_id='branch_task',dag=dag,
		provide_context=True, python_callable=branch_test)