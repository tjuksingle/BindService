# BindService
A simple case which connect service with bind method.
Those first learner may need to view the code.

Step

1.Create a new Service named Myservice which extends Service. press ALT+ENTER (Windows) add following code:
    
    @Override
    public IBinder onBind(Intent intent) {
        return null;}
 
Replace null with MyBinder. Then process step 2. 

2.Create a inner class named LocalBinder which extends Binder (not IBinder). Create a public method named getService:

    public class LocalBinder extends Binder{
        MyService getService() {
            return MyService.this;
        }}
3.Create a global variable MyBinder:

    private LocalBinder myBinder=new LocalBinder();

4.So far, We have done with the basic setting with the Service. At Activity, create those global variable:
    
    private MyService myService;
    private ServiceConnection serviceConnection;
    //private Intent serviceIntent;

5.Override onCreate method, setup onClickListener:

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        serviceIntent = new Intent(MainActivity.this,MyService.class);
        serviceConnection=new ServiceConnection() {
            @Override
            public void onServiceConnected(ComponentName componentName, IBinder iBinder) {
                Log.v(TAG,"onServiceConnected");
                myService=((MyService.LocalBinder)iBinder).getService();
            }

            @Override
            public void onServiceDisconnected(ComponentName componentName) {
                Log.v(TAG,"onServiceDisconnected");
            }
        } ;

        Button buttonStart=(Button)findViewById(R.id.buttonStart);
        Button buttonEnd=(Button)findViewById(R.id.buttonStop);
        Button buttonUsing=(Button)findViewById(R.id.buttonUse);
        
        buttonStart.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                //Intent intent=new Intent(MainActivity.this,MyService.class);
                //startService(intent);
                bindService(serviceIntent,serviceConnection, Service.BIND_AUTO_CREATE);
            }
        });

        buttonEnd.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                MyService = null;
                unbindService(serviceConnection);
            }
        });
        buttonUsing.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if(myService!=null){
                    Log.v(TAG,"Using Service:"+myService.add(1,3));
                }

            }
        });
    }

6.Don't forget release service when activity stop in case you forget press end button:
    
    @Override
    protected void onStop() {
        super.onStop();
        MyService = null;
        unbindService(ServiceConnection);]
        //stopService(serviceIntent);
    }
   
7.Lastly，announce service in the manifest.
        
        <service
            android:name=".MyService"
            android:enabled="true"
            android:exported="true"></service>
