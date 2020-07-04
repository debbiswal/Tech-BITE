CQRS. 

https://debbiswal.github.io/Tech-BITE/. 

```cs
using System;  
using System.Collections.Generic;  
using System.Linq;  

namespace Test1
{
    public class Command : EventArgs
    {
        public bool Register = true;
    }

    public class Query
    {
        public object Result;
    }

    public class Event
    {

    }

    public class EventBroker
    {
        public IList<Event> AllEvents = new List<Event>();
        public event EventHandler<Command> Commands;
        public event EventHandler<Query> Queries;

        public void ExecuteCommand (Command c)
        {
            Commands?.Invoke(this, c);
        }

        public T ExecuteQuery<T>(Query q)
        {
            Queries?.Invoke(this, q);
            return (T)q.Result;
        }

        public void UndoLast()
        {
            var e = this.AllEvents.LastOrDefault();
            var ac = e as AgeChangedEvent;
            if (e!=null)
            {
                this.ExecuteCommand(new ChangeAgeCommand(ac.Target, ac.OldValue) { Register = false }); ;
                this.AllEvents.Remove(e);
            }

        }
    }

    public class Person {
        private int Age;
        private EventBroker eventBroker;

       public Person(EventBroker broker)
        {
            this.eventBroker = broker;
            this.eventBroker.Commands += EventBroker_Commands;
            this.eventBroker.Queries += EventBroker_Queries;
        }

        private void EventBroker_Queries(object sender, Query query)
        {
            var aq = query as AgeQuery;
            if (aq != null && aq.Target == this)
            {                
                aq.Result = this.Age;
            }
        }

        private void EventBroker_Commands(object sender, Command command)
        {
            var cac = command as ChangeAgeCommand;
            if (cac!=null && cac.Target == this)
            {
                if (cac.Register)
                    this.eventBroker.AllEvents.Add(new AgeChangedEvent(this, this.Age, cac.NewAge));

                this.Age = cac.NewAge;
            }
        }
    }

    public class ChangeAgeCommand : Command
    {
        public Person Target;
        public int NewAge;

        public ChangeAgeCommand(Person target,int newAge)
        {
            this.Target = target;
            this.NewAge = newAge;
        }

    }

    public class AgeQuery : Query
    {
        public Person Target;       

        public AgeQuery(Person target)
        {
            this.Target = target;
        }
    }

    public class AgeChangedEvent : Event
    {
        public Person Target;
        public int OldValue,NewValue;
        public AgeChangedEvent(Person person,int oldValue,int newValue)
        {
            this.Target = person;
            this.OldValue = oldValue;
            this.NewValue = newValue;
        }
        public override string ToString()
        {
            return $"Age changed from {OldValue} to {NewValue}";
        }
    }

    class MainClass
    {
        public static void Main(string[] args)
        {
            EventBroker broker = new EventBroker();
            Person p = new Person(broker);
            int age;

            // Change the Age
            broker.ExecuteCommand(new ChangeAgeCommand(p, 40));           
            // Query the age
            age = broker.ExecuteQuery<int>(new AgeQuery(p));
            Console.WriteLine("Changed Age is {0}",age);

            // Change the Age
            broker.ExecuteCommand(new ChangeAgeCommand(p, 50));
            // Query the age
            age = broker.ExecuteQuery<int>(new AgeQuery(p));
            Console.WriteLine("Changed Age is {0}", age);

            // Change the Age
            broker.ExecuteCommand(new ChangeAgeCommand(p, 60));
            // Query the age
            age = broker.ExecuteQuery<int>(new AgeQuery(p));
            Console.WriteLine("Changed Age is {0}", age);

            //Undo last change
            broker.UndoLast();

            // Query the age
            age = broker.ExecuteQuery<int>(new AgeQuery(p));
            Console.WriteLine("Afetr UndoLast , Age is {0}", age);

            //Print All events
            Console.WriteLine("All recorded events(except UndoLast)");
            foreach (var e in broker.AllEvents)
            {
                Console.WriteLine(e);
            }

        }
    }
}
```
