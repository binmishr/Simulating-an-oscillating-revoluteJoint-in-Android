# Simulating-an-oscillating-revoluteJoint-in-Android

In this post I simulate an oscillating revoluteJoint in Android using Box2D physics engine & AndEngine. While I was attempting this in the context of something larger I found this fairly challenging so I decided to write a separate post on this.  Creating a revoluteJoint was rather trivial.  This was done as follows

Initially I created a static point to act as pivot.  I then used a rectangular shape joined at the pivot. I wanted this to oscillate about the pivot. So the pivot & the rectangular shape were created as follows
circle = new Sprite(100, 320, this.mBallTextureRegion, this.getVertexBufferObjectManager());

circleBody = PhysicsFactory.createCircleBody(this.mPhysicsWorld, circle, BodyType.StaticBody, FIXTURE_DEF);

this.mScene.attachChild(circle);

// Create leg

leg = new Sprite(110, 325, this.mLegTextureRegion, this.getVertexBufferObjectManager());

legBody = PhysicsFactory.createBoxBody(this.mPhysicsWorld, leg, BodyType.DynamicBody, gameFixtureDef);

this.mPhysicsWorld.registerPhysicsConnector(new PhysicsConnector(leg, legBody, true, true));

this.mScene.attachChild(leg);

Then I created a revoluteJoint between the leg and the circleBody as follows

final RevoluteJointDef rJointDef = new RevoluteJointDef();

rJointDef.initialize(legBody, circleBody, circleBody.getWorldCenter());

rJointDef.enableMotor = true;

rJointDef.enableLimit = true;

rJoint = (RevoluteJoint) this.mPhysicsWorld.createJoint(rJointDef);

rJoint.setMotorSpeed(2);

rJoint.setMaxMotorTorque(100);

rJoint.setLimits((float)(30 * (Math.PI)/180), (float)(270 * (Math.PI)/180));

The revoluteJoint has a lower limit of 30 degrees and an upper limit of 270 degrees.

To make it oscillate I created a Timer task which I fire every second. Every time the timer fires I reverse the motor speed as follows

class IntervalTimer {

Timer timer;

public IntervalTimer(int seconds, RevoluteJoint rj) {

Log.d(“Inside”,“in”);

timer = new Timer(); //At this line a new Thread will be created

timer.scheduleAtFixedRate(new RemindTask(rj), seconds*1000, 1000);

}

class RemindTask extends TimerTask {

RevoluteJoint rj1;;

RemindTask(RevoluteJoint rj){

rj1 = rj;

}

@Override

publicvoid run() {

Log.d(“x”,“x” +“Reversing motor”);

reverseMotor();

}

publicvoid reverseMotor(){

rj1.setMotorSpeed(-(rj1.getMotorSpeed()));

rj1.setMaxMotorTorque(100);

Log.d(“aa”,“speed:”+rj1.getMotorSpeed() + “torque:” + rj1.getMotorTorque());

}

}

}

I then instantiate the timer

new IntervalTimer(5,rJoint);

I now have an oscillating revoluteJoint which can be used in any number of ways.
