## Using SpyOnService

We can *inject* the sub-service into the service to be tested.

### Injected Service and Main Service
````
@Injectable({ providedIn: 'root'})
export class LoggerService {

  public getName() {
    return 'I am a LoggerService';
  }
}


@Injectable({ providedIn: 'root'})
export class MainService {

  constructor(private logger: LoggerService) { }

 public getName() {
    return 'I am a MainService';
  }

  public loggerOfMain() {
    return `Main: ${this.logger.getName()}`;
  }
}
````

### Service Spec

```
import {async, TestBed} from '@angular/core/testing';

import {LoggerService, MainService} from './main.service';
import {main} from '@angular/compiler-cli/src/main';

describe('Using CreateSpy which uses actual services but overrides the return values', () => {
  let service: MainService;
  let lg1: LoggerService = null;
  // let lg: LoggerService;

  beforeEach(async(
    () => {
      // spyOnInst = jasmine.createSpyObj(['getName']);
      // spyOnInst.and.returnValue('Freddy');
      TestBed.configureTestingModule({
        providers: [
          {provide: LoggerService, useClass: LoggerService},
          {provide: 'T1', useClass: MainService, deps: [LoggerService]}
        ]
      }).compileComponents();
      service = TestBed.get('T1');
      lg1 = TestBed.get(LoggerService);
      lg1.getName = jasmine.createSpy('getName').and.returnValue('timmy');
    }
  ));

  it('should be created', () => {
    expect(lg1).toBeTruthy();
    expect(service.getName()).toEqual('I am a MainService');
    expect(service.loggerOfMain()).toEqual('Main: timmy');
  });
});

describe('Using createSpyObj creates a Mock service', () => {
  let mainservice: MainService;
  let mockLogger: LoggerService;
  beforeEach(() => {
    mockLogger = jasmine.createSpyObj('LoggerService', {getName: 'zippy'});
    TestBed.configureTestingModule({
      providers: [
        {provide: LoggerService, useValue: mockLogger},
        {provide: 'mainservice', useClass: MainService, deps: [LoggerService]}]
    });
    mainservice = TestBed.get('mainservice');
  });

  it('mocklogger should exist', () => {
    expect(mockLogger).toBeTruthy();
    const testval = mockLogger.getName();
    expect(testval).toEqual('zippy', `should be ${testval}`);
  });

  it('mockLogger injected into the mainService and called', () => {
    const testval = mainservice.loggerOfMain();
    expect(testval).toEqual('Main: zippy');
  });
});


```

RESOURCES
https://scriptverse.academy/tutorials/jasmine-spyon.html
