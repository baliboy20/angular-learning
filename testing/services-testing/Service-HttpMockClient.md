## HttpClient injected Services

  
  #### Http Service
  
 ````
import { Injectable } from '@angular/core';
import {HttpClient, HttpClientModule} from '@angular/common/http';
import {of} from 'rxjs';
import {map, tap} from 'rxjs/operators';
const url = 'http://localhost:3000/api';
@Injectable({
  providedIn: 'root'
})
export class RepositoryService {
  constructor(private http: HttpClient) { }
  getSignal(value?) {
   return this.http.get(url)
      .pipe(tap(v => JSON.stringify(v))
      );
  }
}
````


  #### Spec
  
 ````
import {getTestBed, TestBed} from '@angular/core/testing';

import { RepositoryService } from './repository.service';
import {HttpClientTestingModule, HttpTestingController} from '@angular/common/http/testing';
import {Injector} from '@angular/core';

describe('RepositoryService using a mocked HttpClient', () => {
  beforeEach(() => TestBed.configureTestingModule({
    imports: [HttpClientTestingModule],
    providers: [ RepositoryService]
  }));

  it('should Exist', () => {
    const service: RepositoryService = TestBed.get(RepositoryService);
    const controller = TestBed.get(HttpTestingController);
    expect(service).toBeTruthy();
    expect(controller).toBeTruthy();
  });

  fit('HttpTestingController should Exist', () => {
    const service: RepositoryService = TestBed.get(RepositoryService);
    const injector = getTestBed();
    const controller: HttpTestingController = TestBed.get(HttpTestingController);

    expect(controller).toBeTruthy('where is the controller?');
    console.log(controller);
    service.getSignal('')
      .subscribe(a => {
        expect(a).toEqual('ben');
      });
    const req = controller.expectOne('http://localhost:3000/api');
    console.log('url', req.request.url);
    expect(req.request.method).toEqual('GET');
    req.flush('ben');
  });
});
````
