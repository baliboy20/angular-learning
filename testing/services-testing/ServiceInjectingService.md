## Using SpyOnService

We can *inject* the sub-service into the service to be tested.

### Injected Service and Main Service
````
@Injectable({
  providedIn: 'root'
})
export class LoggerService {

  public getName() {
    return 'I am a LoggerService';
  }
}

@Injectable({
  providedIn: 'root'
})
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
import {LoggerService, MainService} from './main.service';

describe('MainService', () => {
  let service: MainService;
  let lg1: LoggerService  = null;

  beforeEach(async(
    () => {
      TestBed.configureTestingModule({
        providers: [
          {provide: LoggerService, useClass: LoggerService},
          {provide: 'T1', useClass: MainService, deps: [LoggerService]}
        ]
      }).compileComponents();
      service = TestBed.get('T1');
      lg1 = TestBed.get(LoggerService);
      lg1.getName =  jasmine.createSpy('getName').and.returnValue('timmy');
    }
  ));

  it('should be created', () => {
    expect(lg1).toBeTruthy();
    expect(service.getName()).toEqual('I am a MainService');
    expect(service.loggerOfMain()).toEqual('Main: timmy');
  });
});

```
