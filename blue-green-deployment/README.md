#Blue-Green Deplyoments

##What is a Blue-Green Deployment?

Blue-green deployment is a release technique that reduces downtime and risk by
running two identical production environments called Blue and Green.

At any time, only one of the environments is live, with the live environment
serving all production traffic.
For this example, Blue is currently live and Green is idle.

As you prepare a new release of your software, deployment and the final stage of
testing takes place in the environment that is *not* live: in this example,
Green.
Once you have deployed and fully tested the software in Green, you switch the
router so all incoming requests now go to Green instead of Blue.
Green is now live, and Blue is idle.

This technique can eliminate downtime due to application deployment.
In addition, blue-green deployment reduces risk: if something unexpected happens
with your new release on Green, you can immediately roll back to the last
version by switching back to Blue.

[Source](https://docs.cloudfoundry.org/devguide/deploy-apps/blue-green.html)

##The challenge:

- Deploy "Version 1" of an application as "Blue"
- Ensure "Blue" is serving traffic
- Deploy "Version 2" of an application as "Green"
- Ensure "Blue" is still serving traffic
- Flip the traffic to "Green" (no client side changes allowed)
- Ensure "Green" is serving traffic and "Blue" is idle
- Deploy "Version 3" of an application as "Blue"
- Ensure "Green" is still serving traffic
- Flip the traffic to "Blue" (no client side changes allowed)
- Ensure "Blue" is serving traffic and "Green" is idle