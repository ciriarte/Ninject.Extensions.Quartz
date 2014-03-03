## Ninject.Extensions.Quartz

Ninject.Extensions.Quartz is an extension that provides a default implementation for the [Quartz.NET](http://quartznet.sourceforge.net/) `ISchedulerFactory`, and optionally provides the `IScheduler` as a singleton.

This extension allows you to use Ninject to resolve `IJob` objects and schedule them with Quartz. Using this extension `IJob` objects scheduled with Quartz can also have dependencies which, when configured correctly, can be resolved by Ninject.

This package contains a `NinjectModule` that has the relevant interfaces pre-registered, so all you have to do is install this package and change your code to use the `NinjectSchedulerFactory`.

To install the package via Nuget: `Install-Package Ninject.Extensions.Quartz`

##Code Examples

Your existing code to schedule jobs using Quartz should look something similar to the following:

    Scheduler = new StdSchedulerFactory().GetScheduler();
    var job = JobBuilder.Create<FollowJob>().Build();
    var trigger =
        TriggerBuilder.Create()
                      .WithSimpleSchedule(
                          x =>
                          x.WithIntervalInMinutes(5)
                           .RepeatForever())
                      .Build();
    Scheduler.ScheduleJob(job, trigger);
    Scheduler.Start();

When Ninject.Extensions.Quartz has been installed, you may use Ninject to resolve jobs. To do this, your code can be changed to:

    var kernel = new StandardKernel();
    kernel.Bind<IJob>().To<SearchJob>();

    var ninjectJobFactory = new NinjectJobFactory(kernel);
    Scheduler = new NinjectSchedulerFactory(ninjectJobFactory).GetScheduler();
    var job = JobBuilder.Create<IJob>().Build();
    var trigger =
        TriggerBuilder.Create().WithSimpleSchedule(x => x.WithIntervalInMinutes(5).RepeatForever()).Build();
    Scheduler.ScheduleJob(job, trigger);
    Scheduler.Start();