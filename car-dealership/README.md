# Nest

**<font size="5">Installation and Commands</font>**

To install nestjs cli: Command Line Interface
```js
npm i -g @nestjs/cli
```

To create a new project
```js
nest new project-name
```
Run the app
```js
yarn start:dev
```
To change Node version, use nvm:
- Go to https://github.com/coreybutler/nvm-windows/releases
- Download the nvm-setup.zip and install it
```js

// Show the options
nvm

// Show the node versions installed
nvm list

// Specify the node version to use
nvm use 15.0.0

// Install a node version
nvm install 17.0.0
```
Some commands
```js
nest generate <command>
nest g <command>
```

Show help with some command:
```js
nest -h
nest g -h
nest g s name -h
```

Common Components
```js
// Create a class
nest g cl <path/name>
// Create a controller
nest g co <path/name>
// Create a decorator
nest g d <path/name>
// Create a guard
nest g gu <path/name>
// Create a interceptor
nest g in <path/name>
// Create a module
nest g mo <path/name>
// Create a pipe
nest g pi <path/name>
// Create a service
nest g s <path/name>
// Create a complete resource
nest g resource <name>

```

Built-in pipes by default
```js
ValidationPipe ParseIntPipe
ParseBoolPipe ParseArrayPipe
ParseFloatPipe ParseUUIDPipe
```

Additional flags
```js
// Confirm what the command will do
nest g s name --dry-run | -d
// No automatic tests file
nest g s name —no-spec
```

Common HTTP methods
```js
Import { Get, Post, Put, Path, Delete } from '@nestjs/common';
```
HTTP method arguments
```js
// Default Get
@Get()
// with dynamic segment
@Get(':id')
// Specifying a route
@Get('cats/breed')
@Get(['cats','breed'])
// Dynamic paths
@Get(':product/:size')
```

Extract information from the request (request)
```js
// get parameters / segments
@Param('id')
// get the request body
@Body()
// get the query parameters
@Query()
// get respose (Express/Fastify)
// Import from express/fastify
@Res()
```

Convert :segment id to integer
```js
import { PipeTransform } from '@nestjs/common';

@Get(':id')
async findOne( @Param('id', ParseIntPipe) id: number ) {
         return this.catsService.findOne(id);
  }
```
Enable CORS
```js
const app = await NestFactory.crate(AppModule);

app.enableCors();
app.enableCors(options);

@WebSocketGateway({ cors: true}); // Socket

```

Cookies: Enable before with its TS definition
```js
yarn add cookie-parser
yarn add -D @types/cookie-parser

npm i cookie-parser
npm i -D @types/cookie-parser
```

**Pipes**
Transform string to the data type you need.
It can be used in the params.

There are four places when you can apply pipes:
- Inside parameters 
```js 
@Patch('id')
updateCar()
@Param('id', ParseIntPipe) id: number)...
```

- Controller level 
```js 
@Controller('cars')
@UsePipes( ValidationPipe)
export class CarsController {
       .
       .
}
```

- Application level (Global Level)
```js 
async function bootstrap() {
  const aap = await NestFactory.create(AppModule);
  app.useGlobalPipes(
    new ValitationPipe({
      whitelist: true,
      forbidNonWhiteListed: true
    })
  );
  .
  .
  .      
}
```
<u>*whitelist*</u> is used to send only the data defined in the DTO. It removes the properties not defined in the DTO.

<u>*forbidNonWhiteListed*</u> returns error if the data does not have the DTO properties.

Apllying both, it will show this messages:

```js
{
  "statusCode": 400,
  "message": [
    "property banana should not exist",
    "property puertas should not exist"
  ],
  "error": "Bad Request"
}
```

```js
Param(property: string, ...pipes: (PipeTransform<any, any> | Type<PipeTransform<any, any>>)[])

@Get(':id')
  getCarById(@Param('id', ParseIntPipe) id: number ) {
    return  this.carsService.findOneById(+id);
  }
```
```js
ValidationPipe
ParseBoolPipe
ParseFloatPipe
ParseIntPipe
ParseArrayPipe
ParseUUIDPipe
```

<font size="4"> **Post, Patch and Delete**</font>

Post is used to create a resource.
The most common ways to send data is JSON, form-data and x-www-form-urlencoded.

```js
import { Body, Controller, Delete, Patch, Post } from '@nestjs/common';
import { CarsService } from './cars.service';

@Controller('cars')
export class CarsController {

  constructor(
    private readOnly carsService: CarsService
  ) {}

  @Post()
  createCar(@Body() body: any) {
    return body;
  }

  @Patch(':id')
  updateCar(
    @Param('id', ParseIntPipe) id: number,
    @Body() body: any ) {
    return body
  }

  @Delete(':id')
  deleteCar(@Param('id', ParseIntPipe) id: number) {
    return {
      method: 'delete',
      id
    };
  }
}
```
Put and patch are used when I want to update a resource.

When using DTOs to validate the model use the validation pipe. It works with the help of *class-validator* and *class-transformer*.

<u>Class Validator</u> help us knowing if something is optional, positive, Mongo Id, array, string, UUID, Decimal, Date string, Boolean, Email, Url
```js
IsOptional        IsPositive              IsMongoId
IsArray           IsString                IsUUID
IsDecimal         IsDate                  ISDateString
IsBoolean         IsEmail                 IsUrl
```
**Using pipe with DTO**
```js
yarn add class-validator class-transformer
```
Controller: In order to apply the DRY (Don't repeat yourself) principle, we set the Validation pipe at a controller level.
```js
import { Controller, ValidationPipe } from '@nestjs/common';

@Controller('cars')
@UsePipes( ValidationPipe )
export class CarsController {

}
```
***DTO Class:***
```js
export class CreateCarDto {

  @IsString()
  readonly brand: string;
  
  @IsString()
  @MinLength(8)
  readonly model: string;
}
```

The DTOs does not change their properties, so we don't want to reassign the value of a DTO accidentally because it could be an error. That's why DTOs should be readonly.

If the DTO was an interface we couldn't do validations.

This validates the name of the properties. If I send moDel it will detect the capital D and will send an error.
We can set custom error message
```js
 @IsString({message:'Should be string'})
```
The pipes can be applied at a parameter level, controller method level, global controller level, global app level.

**Exception Handling**

Nest JS comes with a built-in expections layer which is responsible for processing all unhandled exceptions across an applitacion. When an exception is not handled by your application code, it is caight by this layer, which then automatically send an appropiate user-friendly  response.

This layer wrapps: *guards*, *Before interceptor*, *Pipe*, *controllers*, *After Interceptor*

**Codes**

**1.** Informational responses (100-199).

**2.** Successful responses (200-299):  200 OK, 201 Created, 204 No Content.

**3.** Redirection messages (300-399): 304 Not Modified ( Shows that there is not need to rbroadcast the requested resources.

**4.** Client error responses (400-499): 
    - 400 Bad Request (the specific reason is not known, the user may have sent the wrong parameters).
    - 401 Unauthorized (attempts to access something that requires authentication or requires an API key).
    - 403 Forbidden (The client sends an API key but the service it is accessing requires different permissions, admin feature. I know who you are but you cannot access)
    - 404 Not Found: I am accessing a part of the web app that does not exist.
    - 
**5.** Server error responses (500-599): 500 Internal Server Error. Something broke in the server.


<font size="4"> **Exception Filter**</font>
Nest provides a set of standard exceptions that inherit from the base HttpException. These are exposed from the @nestjs/common package, and represent many of the most common HTTP exceptions:

- <u>BadRequestException</u>
- <u>UnauthorizedException</u>: Client doesn't have the authorization token or it doesn't have the authorization.
- <u>NotFoundException</u>: Could be 404. It didn't find what it was looking for.
- <u>ForbiddenException</u>: the server has received and understood the request, but refuses to send a response.
- <u>NotAcceptableException</u>
- <u>RequestTimeoutException</u>:  It is sent on an idle connection by some servers, even without any previous request by the client.
- <u>ConflictException</u>
- <u>GoneException</u>: 410 access to the target resource is no longer available at the origin server and this condition is likely to be permanent.
- <u>HttpVersionNotSupportedException</u>
- <u>PayloadTooLargeException</u>
- <u>UnsupportedMediaTypeException</u>
- <u>UnprocessableEntityException</u>
- <u>InternalServerErrorException</u>
- <u>NotImplementedException</u>
- <u>ImATeapotException</u>
- <u>MethodNotAllowedException</u>
- <u>BadGatewayException</u>
- <u>ServiceUnavailableException</u>
- <u>GatewayTimeoutException</u>
- <u>PreconditionFailedException</u>

```js
findOneById( id: number) {
  const car = this.cars.find( car => car.id === id);

  if(!car) {
    throw new NotFoundException(`Car with id ${ id } not found`)
  }

  return car;
}
```

```js
{
  "statusCode": 404,
  "message": "Car with id '4' not found",
  "error": "Not found"
}
```



<strong>To create an object</strong>

The only thing controllers do is listening the client requests and returning an answer. It doesn't handle the business logic.

<strong>Controller:</strong>

```js
import { Body, Controller, Delete, Patch, Post } from '@nestjs/common';
import { CarsService } from './cars.service';

@Controller('cars')
export class CarsController {

  constructor(
    private readOnly carsService: CarsService
  ) {}

  @Post()
  createCar( @Body() createCarDto: CreateCarDto) {
    return this.carService.create( createCarDto);
  }
}
```
<strong>Service:</strong>
```js
import { createCarDTO } from './dto/create-car.dto';
import { car } from './interfaces/car.interface';

@Injectable()
export class CarsService() {

  private cars: Car[] = [
    {
      id: uuid(),
      brand: 'Toyota',
      model: 'Corolla'
    }
  ];

 

  create( createCarDto: CreateCarDto) {
    const car: Car = {
      id: uuid(),
     ...createCarDto
    }
    /* The equivalent would be:
    const car: Car = {
      id: uuid(),
     brand: createCarDto.brand,
     model: createCarDto.model,
    }
    */
    this.car.push( car );
    return ;
  }

 /* Another way
   create({ model, brand }: CreateCarDto) {
     const car: Car = {
      id: uuid(),
      brand, 
      model
    }
    return ;
   }
   */

}
```
<strong>DTO class:</strong>
```js
import { IsString } from 'class-validator';

export class CreateCarDTO {

  @IsString()
  readOnly brand: string;

  @IsString()
  readOnly model: string;
  
}

```

<strong>To update an object</strong>

We shouldn't use the same DTO if we just want to update only one property, because using the same createCarDto we should send the entire object.

```js
@Patch(':id')
updateCar(
  @Param('id', ParseUUIDPipe ) id: string,
  @Body() updateCarDto: UpdateCarDto ) {
    
    return updateCarDto;
}
```

<strong>DTO class:</strong>
```js
import { IsString } from 'class-validator';

export class UpdateCarDto {

  @IsString()
  @IsUUID()
  readonly id: string;
  
  @IsString()
  @IsOptional()
  readOnly brand?: string;

  @IsString()
  @IsOptional()
  readOnly model?: string;
  
}

```

Create an index.ts file in the DTOs folder to have all the imports in there:

```js
export { UpdateCarDto } from './update-car.dto';
export { CreateCarDto } from './create-car.dto';
```

<strong>Service:</strong>
```js
import { CreateCarDTO, UpdateCarDto } from './dto';
import { car } from './interfaces/car.interface';

@Injectable()
export class CarsService() {

  private cars: Car[] = [
    {
      id: uuid(),
      brand: 'Toyota',
      model: 'Corolla'
    }
  ];

  updateCar(id: string, updateCarDto: UpdateCarDto) {
    let carDB = this.findOneById( id );

    if( updateCarDto.id && updateCarDto.id !== id)
      throw new BadRequestException(`Car id is not inside body`);

    this.cars = this.cars.map( car => {

      if( car.id === id) {
        carDB = {...carDB, ...updateCarDto, id}
        return carDB;
      }

      return car;
    });

    return carDB;
  }


```

<strong>To delete an object</strong>

We have to validate that the car exists, otherwise it would be a false positive.

Service:
```js
  delete(id: string) {
    const car = this.findOneById( id );
  }

```
Controller:
```js
  @Delete(':id')
  deleteCar( @Param('id', ParseUUIDPipe ) id: string) {
    return this.carService.delete(id)
  }
```

<font size="4">Nest CLI Resources</font>

To generate a new CRUD without tests:
```js
nest g res brands --no-spec
  What transport layer do you use?
    > REST API
  Would you like to generate CRUD entry points? Yes
```

It creates 
- brands.controller.
- brands.module.
- brands.service.
- dto/create-brand.dto.ts
- dto/update-brand.dto.ts
- entities/brand.entity.ts

<font size="4">To  serve static content:</font>
```js
yarn add @nestjs/serve-static
```

In the app.module.ts
```js
import { join } from 'path';
import { MongooseModule } from '@nestjs/mongoose';
import { Module } from '@nestjs/common';
import { ServeStaticModule } from '@nestjs/serve-static';

@Module({
  imports: [
    ServeStaticModule.forRoot({
      rootPath: join(__dirname,'..', 'public'),
    }),
  ],
})
```

<font size="4"><strong>Connect nest with Mongo:</strong></font>
```
npm install --save @nestjs/mongoose mongoose
or
yarn add @nestjs/mongoose mongoose
```

In the app.module.ts
```js
import { join } from 'path';
import { MongooseModule } from '@nestjs/mongoose';
import { Module } from '@nestjs/common';
import { ServeStaticModule } from '@nestjs/serve-static';

@Module({
  imports: [
    ServeStaticModule.forRoot({
      rootPath: join(__dirname,'..', 'public'),
    }),
    MongooseModule.forRoot('mongodb://localhost:27017/dbName')
  ],
})
```

Create a file named docker-compose-yaml

```
version: '3'

services:
  db:
    image: mongo:5
    restart: always
    ports:
      - 27017:27017
    environment:
      MONGODB_DATABASE: nest-pokemon
    volumes:
      - ./mongo:/data/db
```
Run the following command:

```
docker-compose up -d
```

Open docker desktop to see the DB running.

<font size="4"><strong>Creating schemas and models</strong></font>

The entity should be a class because through classes we can start defining business rules. Usually, the entities make reference of how we'll want to save exactly in our database.

```js
import { Document, SchemaFactory } from 'mongoose';

@Schema()
export class Pokemon extends Document {

  // id: string // Mongo gives it to me.
  @Prop({
    unique: true,
    index: true,
  })
  name: string;

  @Prop({
    unique: true,
    index: true,
  })
  no: number;
}

export const PokemonSchema = SchemaFactory.createForClass( Pokemon );
```

When extending "Document", it adds all the functionalities, names methods, etc.

Adding @Schema() decorator to indicate that it is a schema of database. It is on nestjs.

Exporting the schema will tell to the database "this are the definitions I want to use, these are the columns, the rules, etc." 

```js
import { MongooseModule } from '@nestjs/mongoose';
import { Module } from '@nestjs/common';
import { Pokemon, PokemonSchema } from './entities/pokemon.entity';

@Module({
  imports: [
    MongooseModule.forFeature([
      {
        name: Pokemon.name,
        schema: PokemonSchema
      }
    ])
  ],
})
```

Creating in Database: In the service

```js
import { BadRequestException, InternalServerErrorException, Body, Post } from '@nestjs/common';
import { Injectable } from '@nestjs/common';
import { InjectModel } from '@nestjs/mongoose';
import { Model } from 'mongoose';

@Injectable()
export class PokemonService {

  constructor(
    @InjectModel(Pokemon.name )
    private readonly pokemonModel: Model<Pokemon>
  ) {}

  create(@Body() createPokemonDto: CreatePokemonDto) {
    try {
      const pokemon = await this.pokemonModel.create( createPokemonDto );
      return pokemon; 

    } catch (error) {
      
      if(error === 11000) {
        throw new BadRequestException(`Pokemon exists in db ${JSON.stringify(error.keyValue)}`)
      }
      console.log(error);
      throw new InternalServerErrorException(`Can't create Pokemon - Check server logs`);
    }
    
  }
}
```

We add the try catch to get the error of repeated object.

We can change the response code if we wish. In the controller add the following:

```js
import { BadRequestException, InternalServerErrorException, HttpCode, HttpStatus, Body, Post } from '@nestjs/common';
  .
  .

  @Post()
  @HttpCode( HttpStatus.UNAUTHORIZED)
  create(@Body() createPokemonDto: CreatePokemonDto) {
  .
  .

```

<font size="4"><strong>FindOneBy</strong></font>

In the controller
```js
@Get(':term')
findOne(@Param('term') id: string) {
  return  this.pokemonService.findOne( term );
}
```

In the service
```js
import { NotFoundException } from '@nestjs/common';
import { Model, isValidObjectId } from 'mongoose';
  .
  .
 constructor(
    @InjectModel(Pokemon.name )
    private readonly pokemonModel: Model<Pokemon>
  ) {}
  .
  .

async findOne( term: string) {
  
  let pokemon: Pokemon;

  // Evaluates if it's not a number
  if( !isNaN(+term)) {
    pokemon = await this.pokemonModel.findOne({ no: term })
  }

  // MongoID

  if(!pokemon && isValidObjectId( term )) {
    pokemon = await this.pokemonModel.findById( term );
  }

  // Name
  if( !pokemon ) {
    pokemon = await this.pokemonModel.findOne( { name: term.toLowerCase().trim() });
  }

  if( !pokemon )
    throw new NotFoundException(`Pokemon with id, name or no "${ term }" not found`);

  return pokemon;
}
```

<font size="4"><strong>Update</strong></font>

Controller:
```js
@Patch(':term')
update(@Param('term') term: string, @Body() updatePokemon: UpdatePokemon) {

  return this.pokemonService.update(term, updatePokemon);
}


```

Here is where extending PartialType in the DTO helps us because it set every property of the object to optional, so we don't have to create the updatePokemon with all its properties.

```js
export class UpdatePokemonDto extends PartialType(CreatePokemon) {}

```

Service:
```js
// We don't know if term is the name, id or number
async update(term: string, updatePokemonDto: UpdatePokemonDto) {
  // Reuse the method findOne. Look above
  const pokemon = this.findOne( term );

  if( updatePokemonDto.name )
    updatePokemonDto.name = updatePokemonDto.name.toLowerCase();
  
  // not setting new to true the object will be always the old object. It will return the last value without the new parameter
  // const updatedPokemon = await pokemon.updateOne( updatePokemon, { new: true});
  // With the line above I would get the serialization of the pokemon and not the updatedPokemon

  try {

    await pokemon.updateOne( updatePokemon, { new: true})
    // Spread the properties and overrides the updatePokemonDto's properties.
    return {...pokemon.toJson(), ...updatePokemonDto };

  } catch(error) {
    this.handleExceptions( error );
  }

}


```

A better approach would be to create a method in the service to handle the exceptions

```js
// Not controlled exceptions
private handleExceptions( errors: any) {
  if(error.code === 11000) {
      throw new BadRequestException(`Pokemon exists in db ${ JSON.stringify(error.keyValue)}`);
    }
    console.log(error);
    throw new InteralServerErrorException(`Can't create Pokemon - Check server logs`);

}
```

<font size="4"><strong>Delete</strong></font>

Controller
```js
@Delete(':id') 
remove(@Param('id') id: string) {
  return this.pokemonService.remove( id );
}
```

Service

```js
async remove(id: string) {
  const pokemon = await this.findOne( id );
  await pokemon.deleteOne();
}
```

<font size="4"><strong>Custom Pipes</strong></font>

To create the pipes, first of all create a module inside src  and the pipe with the following command:

```js
nest g mo common

nest g pi common/pipes/parseMongoId --no-spec
```
I will create a file named parse-common-id.pipe.ts

```js
import { ArgumentMetadata, Injectable, PipeTransform } from '@nestjs/common';


@Injectable()
export class ParseMongoIdPipe implements PipeTransform {
// evaluates if the value is a MongoID
  transform(value: string, metadata: ArgumentMetadata) {

    if(!IsValidObjectId(value)) {
      throw new BadRequestException(`${value} is not a valid MongoID`)
    }

    return value;
  }
}
```

Controller
```js
@Delete(':id') 
remove(@Param('id', ParseMongoIdPipe) id: string) {
  return this.pokemonService.remove( id );
}
```

Validate and delete in one query:

```js
async remove(id: string) {
  // const pokemon = await this.findOne( id );
  // await pokemon.deleteOne();
  // const result = await this.pokemonModel.findByIdAndDelete( id );
  const result = this.pokemonModel.deleteOne( { _id: id});
}
```
The respond would be:

```js
{
  "acknowledged": true,
  "deletedCount": 0
}
```

if deletedCount is 0 means it didn't delete anything.

```js
async remove(id: string) {
  // const pokemon = await this.findOne( id );
  // await pokemon.deleteOne();
  // const result = await this.pokemonModel.findByIdAndDelete( id );
  const { deletedCount } = this.pokemonModel.deleteOne( { _id: id});
  if ( deletedCount === 0 )
    throw new BadRequestException(`Pokemon with id "${id}" not found `);

  return;
}
```

<font size="4"><strong>Pagination</strong></font>

Controller:
```js
@Get()
findAll() {
  return this.pokemonService.findAll();
}
```

We need a new DTO to know the name of the parameter, if the parameter will be a number, if it should be positive, etc.

Create a DTO in the common folder pagination.dto.ts:
Controller:
```js
import { isOptional, IsPositive, Min } from 'class-validator';

export const PaginationDto {
  
  @IsOptional
  @IsPositive
  @Min(1)
  limit?: number;

  @IsOptional
  @IsPositive
  offset?: number;
}
```

Controller: With the <strong>@Query()</strong> decorator I can get all the parameters coming in the request.
```js
@Get()
findAll(@Query() paginationDto: PaginationDto) {
  return this.pokemonService.findAll( paginationDto );
}
```

Service:
```js

findAll( paginationDto ) {
  const { limit = 10, offset = 0 } = paginationDto; // set default values if there is not values coming
  return this.pokemonModel.find()
  .limit( limit ) // number of items
  .offset( offset ) // where it begins or jumps from
  .sort( { // ascendant order with the column no of the database
    no: 1
  })
  .select('-__v'); // removes with - the parameter __v
}
```

There is an error when we send limit=10 because it is a string:

```js
{
  "message": [
    "limit must not be less than 1",
    "limit must be positive number"
  ]
}
```

We can transform the Dtos in the globalPipes with the data type we are waiting.
In the main.ts add the following parameters:
```js
 transform: true,
 transformOptions: {
    enableImplicitConversion: true,
  }
```

```js
async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.setGlobalPrefix('api/v2');

  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      forbidNonWhitelist: true,
      transform: true,
      transformOptions: {
        enableImplicitConversion: true,
      }
    }
    )
  )
}
```


<font size="4"><strong>TypeORM</strong></font>

- Install and configure Postgres:
  Create a file named "docker-compose.yaml"
  
```js
version: '3'

services:
  db:
    image: postgres:14.3
    restart: always
    ports:
      - "5432:5432"
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: ${DB_NAME}
    container_name: whateverdb
    volumes:
      - ./postgres:/var/lib/postgresql/data
 ```
Now run:
 ```js
docker-compose up
 ```
To install <strong>TypeORM</strong>
  ```js
npm install save @nestjs/typeorm typeorm pg
npm install @nestjs/config
 ```
pg is the drive who will help typeORM to connect to the database. You can use mysql2 as well.

<strong>To connect Postgres</strong>

In the .env file:
  ```js
DB_PASSWORD=mYsECR3Tp4SS
DB_NAME=TesloDB
DB_HOST=5432
DB_USERNAME=postgres
 ```
.env files does not upload so we have to add them to the .gitignore.

in the app.module.ts :
```js
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config';

@Module({
  imports: [
    ConfigModule.forRoot(), // it should be at the beginning. It exposes a ConfigService which loads the appropriate .env file

    TypeOrmModule.forRoot({
      type:'postgres',
      host: process.env.DB_HOST,
      port: +process.env.DB_PORT,
      database: process.env.DB_USERNAME,
      username: process.env.DB_USERNAME
      password: process.env.DB_PASSWORD,
      autoLoadEntities: true,
      synchronize: true// Automatically synchornizes the database if I make some change in the entity. It is not used in production.
    })
  ]
})
 ```
The entities are a representation of an object in the database. An entity would be a table.

```js
import { Column, Entity, PrimaryGeneratedColumn } from 'typeorm';

@Entity()
export class Product {

  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column('text', {
    unique: true,
  })
  title: string;

  @Column('numeric', {
    default: 0,
  })
  price: number;

  @Column({
    type: 'text',
    nulllable: true
  })
  description: string;

  @Column('text', {
    unique: true
  })
  description: number;

  @Column('int', {
     default: 0,
  })
  stock: number;

  @Column('text', {
     array: true
  })
  sizes: string[]

  @Column('text')
  gender: string;

}

```
The column has some arguments such as the type of data (text, bigint, binary, bool, boolean...). Some rules can be added as well, e.g title should be unique, or the default value should be 0. We have to be careful because there are some type of data that postgres does not accept such as using number instead of numeric.

There is another way to specify the column attributes using the type option:
```js
 @Column({
    type: 'text',
    nulllable: true
  })
  description: string;
```


We should make an import with all the entities the module is defining.

In the products.module.ts:

```js
import { TypeOrmModule } from '@nestjs/typeorm';
import { Module } from '@nestjs/common';
import { ProductsService } from './products.service';
import { ProductsController } from './products.controller';
import { Product } from './products.controller';

@Module({
  controllers: [ ProductsController ],
  providers: [ProductService],
  imports: [
    TypeOrmModule.forFeature([ product ])
  ]
})
export class ProductsModule {}

```

The database will be updated every time we save changes from the entity, if we change a column it will be changed immediately in the database.

To set a route (e.g. 'api/products') and and the DTOs configuration:

```js
async function bootstrap() {
  .
  .
  app.setGlobalPrefix('api'); // route
  app.useGlobalPipes(
    new ValitationPipe({
      whitelist: true,
      forbidNonWhiteListed: true
    })
  );
  .
}
```
Since I'm not waiting the id because the database will generate it I won't add it to the Dto.
```js
import { IsArray, IsIn, IsNumber, IsOptional, IsPositive, IsString, Minlength } from 'class-validator';
export class CreateProductDto {
  
  @IsString()
  @MinLength(1)
  title: string;

  @IsNumber()
  @IsPositive()
  @IsOptional()
  price?: number;

  @IsString()
  @IsOptional()
  description?: string;
  
  @IsString()
  @IsOptional()
  slug?: string;

  @IsInt() // No decimals
  @IsPositive()
  @IsOptional()
  stock?: number;

  @IsString({ each: true }) // each of the products most be a string
  @IsArray()
  sizes: string[];

  @IsIn(['men', 'women', 'kid', 'unisex']) // To be inside an array that will have these values. If these values aren't inside the Dto won't allow it.
  gender: string;
}
```

<strong>Insert using TypeORM</strong>

If we define the instance by creating a new one with new Product() we would have to control more things, maybe we need to verify if the Product have been modified in one or two or three tables so it's more easy to do it with <strong>repository pattern</strong>

In products.service.ts

```ts
import { InjectRepository } '@nestjs/typeorm';
import { Product } './entities/product.entity';

  .
  .
  constructor(
    @InjectRepository(Product)
    private readonly productRepository: Repository<Product>,
  ) {}

  async create(createProduct: CreateProductDto) {
    try {

      const product = this.productRepository.create(createProductDto);
      await this.productRepository.save( product );
    } catch (error) {
      console.log(error);
      throw new InternalServerErrorException('help!');
    }
  }

```
This repository not only works for inseting, it also works for making query builders and transactions, rollbacks and a bunch of more things.


<strong>Erros handler</strong>

```js
import { Injectable, InternalServerErrorException, Logger } from '@nestjs/common';
  .
  .
@Injectable()
export class ProductsService {

    private readonly logger = new Logger('ProductsService');
    .
    .

     async create(createProduct: CreateProductDto) {
    try {

      const product = this.productRepository.create(createProductDto);
      await this.productRepository.save( product );

      return product;
    } catch (error) {
      this.handleDBExceptions(errors);
    }

    private handlExceptions( error: any ) {
      if( error.code === '23505' )
        throw new BadRequestException(error.detail);

      this.logger.error(error);
      throw new InternalServerErrorException('Unexpected error, check server logs');
    }
}
```

<strong>BeforeInsert and BeforeUpdate</strong>

In some cases we need to fill a property of the object when the user sends it empty. An ugly way to do it is:

```js
 async create(createProduct: CreateProductDto) {
    try {
       if( !createProductDto.slug) {
          createProductDto.slug = createProductDto.title
        }

        createProductDto.slug = createProductDto.slug
          .toLowerCase()
          .replaceAll(' ', '_')
          .replaceAll("'", '')

        const product = this.productRepository.create(createProductDto);
        await this.productRepository.save( product );

        return product;
      } catch (error) {
        this.handleDBExceptions(errors);
        }
 }
```

A better way is to create a method inside the entity with the BeforeInsert notation:

```js
import { BeforeInsert, Column, Entity, PrimaryGeneratedColumn } from 'typeorm';

@Entity()
export class Product {
  .
  .
  .

  @Column('text', {
     array: true
  })
  sizes: string[]

  @Column('text')
  gender: string;

  @BeforeInsert()
    checkSlugInsert() {
      if( !this.slug) {
          this.slug = this.title
      }
      this.slug = this.slug
        .toLowerCase()
        .replaceAll(' ', '_')
        .replaceAll("'", '')
    }


}

```

<strong>Get and Delete TypeORM</strong>

Service:
```js
  .
  .
  .
async findOne( id: string) {
  const product = await this.productRepository.findOneBy({ id });

  if( !product )
    throw new NotFoundException(`Product with id ${ id } not found`);

  return product;
}

async remove( id: number, updateProductDto: UpdateProductDto ) {
  const product = await this.findOne( id );

  await this.productRepository.remove( product );

}
```

Controller:
```js
  .
  .
  .
  @Get(':id')
  findOne(@Param('id', ParseUUIDPipe ) id: string) {
    return this.productService.findOne( id );
  }

  @Delete(':id')
  remove(@Param('id', ParseUUIDPipe ) id: string) {
    return this.productService.remove(id);
  }
```

<strong>Paging in TypeORM</strong>

We should create a Dto and since it's a common task it should be created in the common folder.

pagination.dto.ts

```js
import { Type } from 'class-transformer';

export class PaginationDto {

  @IsOptional
  @IsPositive
  @Type( () => Number ) // it replaces enableImplicitConversions if we don't set it with useGlobalPipes validationPipe
  limit?: number;

  @IsOptional
  //@IsPositive 0 is not considered as a positive number
  @Min(0)
  @Type( () => Number ) // it replaces enableImplicitConversions if we don't set it
  offset?: number;
}
```

Controller:

```js
import { Query } from '@nestjs/common';

@Get()
findAll( @Query() paginationDto: PaginationDto ) {
  return this.productService.findAll( paginationDto );
}

```

Service:
```js



findAll( paginationDto: PaginationDto ) {
  
  const {limit = 10, offset = 0 } = paginationDto;

  return this.productRepository.find({
    take: limit,
    skip: offset,
    // TODO relaciones
  })
}

```

<strong>Find by a property (slug) or UUID at the same time</strong>



```js
yarn add uuid
yarn add -D @types/uuid
```
Controller:

```js
import { Query } from '@nestjs/common';

@Get(':term')
findOne( @Param('term') term: string ) {
  return this.productService.findOne( term );
}

```

Service:

```js
import { validate as isUUID } from 'uuid';

async findOne( term: string ) {
  
  let product: Product;

  if ( isUUID(term) ) {
    product = await this.productRepository.findOneBy( { id: term });
  } else {
    product = await this.productRepository.findOneBy( { slug: term });
  }

  if( !product )
    throw new NotFoundException(`Product with ${ term } not found`);
  
  return product;
}

```

<strong>QueryBuilder</strong>

It is a method or function that allows to create queries

```js
import { validate as isUUID } from 'uuid';

async findOne( term: string ) {
  
  let product: Product;

  if ( isUUID(term) ) {
    product = await this.productRepository.findOneBy( { id: term });
  } else {
    const queryBuilder = await this.productRepository.createQueryBuilder();
    product = await queryBuilder.where(' UPPER(title) =:title or slug =:slug', {
      title: term.toUpperCase(),
      slug: term.toLowerCase()
    }).getOne();
  }

  if( !product )
    throw new NotFoundException(`Product with ${ term } not found`);
  
  return product;
}

```

<strong>Update in TypeORM</strong>

Controller
```js
@Patch(':id')
update(
  @Param('id', ParseUUIDPipe) id: string, 
  @Body() updateProductDto: UpdateProductDto
  ) {
  return this.productService.update(+id, updateProductDto);
}
```

Service
```js
async update( id: string, updateProductDto: UpdateProductDto) {

  const product = await this.productRepository.preload({
    id: id,
    ...updateProductDto
  })

  if( !product ) throw new NotFoundException(`Product with id: ${ id } not found`);

  try {
    await this.productRepository.save( product );
    return product;

  } catch(error) {
    this.handleDBExceptions(error);
  }

}
```

preload: TypeORM looks for a product with the specified id and load all the properties that are in the updateProductDto the method is getting. It doesn't update it, it prepares it for the update

<strong>BeforeUpdate</strong>

Dto
```js
import { BeforeInsert, Column, Entity, PrimaryGeneratedColumn } from 'typeorm';

@Entity()
export class Product {
  .
  .
  @Column('text', {
    unique: true
  })
  slug: string;
  .
  .
  .

  @BeforeInsert()
    checkSlugInsert() {
      if( !this.slug) {
          this.slug = this.title
      }
      this.slug = this.slug
        .toLowerCase()
        .replaceAll(' ', '_')
        .replaceAll("'", '')
    }

  @BeforeUpdate()
  checkSlugUpdate() {
    this.slug = this.slug
        .toLowerCase()
        .replaceAll(' ', '_')
        .replaceAll("'", '')
  }

}

```

BeforeInsert is used when we want to create an object and there is one property that needs to be filled by us.

BeforeUpdate is used when we want to update an object and this property is not filled.

<strong>Add New Column</strong>

After adding it to our entity and Dto we check in the database the property added.

<hr />
<hr />
<font size="4"><strong>TypeORM Relations</strong></font>

We need to add an Image entity. The relation with product is ManyToOne since we have many images to one product.

```js
import { Entity, PrimaryGeneratedColumn } from 'typeorm'

export class ProductImage {

  @PrimaryGeneratedColumn()
  id: number;

  @Column('text')
  url: string;

  @ManyToOne(
    () => Product,
    ( product ) => product.images
  )
  product: Product
}

```

We need to add it to product.module.ts

```js
  .
  .
  .
  imports: [
    TypeOrmModule.forFeature([ Product, ProductImage ])
  ]
```

We need to create a relation between Product and ProductImage. Since one product can have many images it would be oneToMany relation.

```js
  .
  .
  .
  @OneToMany(
    () => ProductImage,
    (productImage) => productImage.product,
    { cascade: true, eager: true } // Every time we use a find method to load a product it will load automatically the images
    // eager and lazy relations https://typeorm.io/eager-and-lazy-relations 
  )
  images?: ProductImage[]
```

Now add it to the Dto
```js
  
  @IsString({ each: true })
  @IsArray()
  @IsOptional()
  images?: string[];


```

To solve the problem go to services
```js

constructor(
  
  @InjectRepository(Product)
  private readonly productRepository: Repository<Product>,

 // add next lines
  @InjectRepository(ProductImage)
  private readonly productImageRepository: Repository<ProProductImageduct>,
)
  async update(id: string, updateProductDto: UpdateProductDto ) {
  const product = this.productRepository.preload({
    id: id,
    ...updateProductDto,
    images: [], // Add this line
  })
    .
    .
  }

  async create(createProductDto: CreateProductDto) {
    
    try {
      const { images = [], ...productDetails } = createProductDto; // if there are no images then it will be []
      const product = this.productRepository.create({
        ...productDetails,
        images: images.map( image => this.productImageRepository.create({ url: image }))
      })
      
      await this.productRepository.save( product ); // saves the product and the images.
      
      return {...product, images};

    } catch (error) {
      this.handleExceptions(error);
    }
  }
```

<strong>Flattening Images</strong>

If I don't want the images to be returned with all the information but only with the url:

```js
async findAll( paginationDto: PaginationDto ) {
  
  const { limit = 10, offset = 0 } = paginationDto;

  const products = await this.productRepository.find( {
    take: limit,
    skip: offset,
    relations: { // Add this line
      images: true,
    }
  })
  
  return products.map( product => ({
    ...product,
    images: product.images.map( img => img.url)
  }))

  // Another way to the last line is
  /*
  return products.map( ({ images, ...rest }) => ({
    ...rest,
    images: images.map( img => img.url )
  }))
  */
}
```

Since we are retrieving the product with createQueryBuilder it won't load the images (we should do a leftJoinAndSelect) to load the image relation.

Controller
```js
@Get(':term')
findOne(@Param( 'term' ) term: string) {
  return this.productService.findOnePlain( term );
}
```

```js
import { validate as isUUID } from 'uuid';

async findOne( term: string ) {
  
  let product: Product;

  if ( isUUID(term) ) {
    product = await this.productRepository.findOneBy( { id: term });
  } else {
    const queryBuilder = await this.productRepository.createQueryBuilder('prod');
    product = await queryBuilder.where(' UPPER(title) =:title or slug =:slug', {
      title: term.toUpperCase(),
      slug: term.toLowerCase()
    })
    .leftJoinAndSelect('prod.images', 'prodImages')
    .getOne();
  }

  if( !product )
    throw new NotFoundException(`Product with ${ term } not found`);
  
  return product;
}

async findOnePlain( term: string) {
  const { images = [], ...rest } = await this.findOne( term ); // if there are no images then images is []
  return {
    ...rest,
    images: images.map( image: => image.url );
  }
}
```

Set an alias inside the createQueryBuilder to specify into the leftJoinAndSelect which is the relation I want to join. 

I can set another alias inside leftJoinAndSelect in case I have more relations with images.

<font size="3"><strong>Query Runner</strong></font>

When we're updating one value of the product with patch method the reference (productId uuid of the product in image table) of the images are deleted, that's because we have a cascade update and in the update method we have an empty array for the images.



If I want to save some images of an specific product and delete all the last images it had, I would have to execute two transactions that are not interconectadas, the elimination and the product update. If one of the two fails I want to reverse the changes and send an error to the user showing what happened.

<strong>Query Runner</strong> allows me to execute x quantity of instructions and tell the query explicitly impact the database.

```js
import { DataSource, Repository } from 'typeorm';
constructor(
  
  @InjectRepository(Product)
  private readonly productRepository: Repository<Product>,

  @InjectRepository(ProductImage)
  private readonly productImageRepository: Repository<ProProductImageduct>,

  // Add this line
  private readonly dataSource: DataSource,
)
  async update(id: string, updateProductDto: UpdateProductDto ) {

    const { images, ...toUpdate } = updateProductDto;

    const product = this.productRepository.preload({ id, ...toUpdate })
    
    if( !product ) throw new NotFoundException(`Product with id: ${ id } not found`);

    // if there are images we have to delete them
    // Create Query Runner
    const queryRunner = this.dataSource.createRunner();
    await queryRunner.connect(); // connects to the DB
    await queryRunner.startTransaction(); // starts the transaction

    

    try {
      // If we have images I want to delete them all.
      if( images ) {
        await queryRunner.manager.delete( ProductImage, { product: { id }}) // deletes all the images which column is productId (id)
        // queryRunner.manager.delete() deletes the registry of the DB. The id is productId in images.
        // queryRunner.manager.softDelete() marks a column that says it is deleted but actually it exists.

        product.images = images.map( 
          image => this.productImageRepository.create({ url: image })
        )
      };

      await queryRunner.manager.save( product );

      await queryRunner.commitTransaction();
      await queryRunner.release();

      return this.findOnePlain( id ); // change it by calling findOnePlain
    
    } catch (error) {

      await queryRunner.rollbackTransaction();
      await queryRunner.release();
      this.handleDBExceptions(error);
    }
  }
```

<strong>Rollback</strong> is to reverse the changes.
<strong>Commit</strong> is to apply the changes.

Every time a transaction is used, we have to make sure whether commiting or rolling back and also release the query because otherwise it will keep the connection in the queryRunner.

If one of both queryRunner.manager.delete or queryRunner.manager.save fails I have to reverse the transaction.

<font size="3"><strong>Cascade Delete</strong></font>

If we try to delete a product we we'll see

```
update or delete on table "product" violates foreign key constraint "FK_" on table "product_image"
```
We are trying to delete a product that has relation with images which exist yet.

```js
async remove(id: string) {
  const product = await this.findOne();
  await this.productRepository.remove(product)
}
```

product-image.entity.ts

```js
  .
  .
  @ManyToOne(
    () => Product,
    ( product ) => product.images,
    { onDelete: 'CASCADE' } // When the product is deleted I want the images to be deleted in cascade.
  )

```
Deletes all the products with its images.

```js

async deleteAllProducts() {
  const query = this.productRepository.createQueryBuilder('product');

  try {
    return await query
    .delete()
    .where({})
    .execute();
  } catch (error) {
    this.handleDBExceptions(error);
  }
}

```

<strong>Insert massive scale</strong>

```js

  private async insertNewProducts() {
    await this.productService.deleteAllProducts();

    const products = initialData.products;

    const insertPromises = [];

    products.forEach( product => {
      insertPromises.push( this.productService.create( product ));
    });

    await promise.all( insertPromises ); // waits for all the promises above are resolved and when they get solved continues with next line

    return true;
  }

```

<font size="3"><strong>Rename Tables</strong></font>

We can change it in the entity filling the notation with the name as follows:
```js

@Entity({ name: 'products' })
export class Product {
    .
    .

}
```

```js

@Entity({ name: 'product_images' })
export class ProductImage {
  .
  .
  
}
```
<hr />
<hr />

<font size="3"><strong>Loading Files</strong></font>

```js
npm i -D @types/multer

```

If we want to upload files we should do it with POST to be sure that the file comes how I want it to comes.

```js

import { Controller, Post, UploadFile, UseInterceptors } from '@nestjs/common';
import { FileInterceptor } from '@nestjs/platform-express'; // it only works if I'm working with express in the transformer. It doesn't work with fastify.
import { FilesService } from './files.service';

@Controller('files')
export class FilesController {
  constructor(private readonly filesService: FileService ) {}

  @Post('product')
  @useInterceptors( FileInterceptor('file')) // file is the name of the parameter I'm sending.
  uploadProductImage( file: Express.Multer.File,) {
    return file;
  }
}

```

<strong>Validating Files</strong>

I need to validate if it is a pdf or an image or a document.

in this case it only can upload images.

Create a folder called 'helpers' and a file inside it called fileFilter.helper.ts

```js
export const fileFilter = ( req: Express.Request, file: Express.Multer.File, callback: Function) => {
  console.log({ file });
  if( !file ) return callback( new Error('File is empty'), false );

  const fileExtension = file.mimetype.split('/')[1];
  const validExtensions = ['jpg', 'jpeg', 'png', 'gif'];

  if( validExtensions.includes( fileExtension )) {
    return callback( null, true);
  }

  callback(null, true);
}

```


```js

import { Controller, Post, UploadFile, UseInterceptors, BadRequestException } from '@nestjs/common';
import { FileInterceptor } from '@nestjs/platform-express'; // it only works if I'm working with express in the transformer. It doesn't work with fastify.
import { FilesService } from './files.service';
import { fileFilter } from './helpers/fileFilter.helper';

@Controller('files')
export class FilesController {
  constructor(private readonly filesService: FileService ) {}

  @Post('product')
  @useInterceptors( FileInterceptor('file', {
    fileFilter: fileFilter // call the helper validator
  })) // file is the name of the parameter I'm sending.
  uploadProductImage( file: Express.Multer.File,
  ) {

    if( !file ) {
      throw new BadRequestException('Make sure that file is an image');
    }
    
    return {
      fileName: file.originalname;
    }
  }
}

```


<strong>Saving image in filesystem</strong>

We can create a public folder and save the image there. But there's a problem, this is a public folder and everybody can see it.

Create a folder named static in the root of the project.

Multer is a package that is used by nest internally. So there's no need to import it, but you can do it anyway.

Create a file in the helper folder named fileNamer.helper.ts

```js
import { v4 as uuid } from 'uuid';

export const fileNamer = (req: Express.Request, file: Express.Multer.File, callback: Function ) => {

  if( !file ) return callback( new Error('File is empty'), false );

  const fileExtension = file.mimetype.split('/')[1];

  const fileName = `${ uuid() }.${ fileExtension}`;

  callback(null, fileName );
}

```


```js
import { fileFilter } from './helpers/fileFilter.helper';
import { fileNamer } from './helpers/fileNamer.helper';
import { diskStorage } from 'multer';
  .
  .
   @Post('product')
  @useInterceptors( FileInterceptor('file', {
    fileFilter: fileFilter,
    storage: diskStorage({
      destination: './static/products',
      filename: fileNamer // use it from helpers
    })
  })) 
  uploadProductImage( file: Express.Multer.File,
  ) {

    if( !file ) {
      throw new BadRequestException('Make sure that file is an image');
    }
    
    return {
      fileName: file.originalname;
    }
  }

```

<strong>Serve files</strong>

@Res is used when I want to take control of the response and not Nestjs. So I have to make the response manually, otherwise the application will be blocked.

When using @Res we skip Global interceptors and certain restrictions that nest uses, certain life cycle steps.

```js
import { fileFilter } from './helpers/fileFilter.helper';
import { fileNamer } from './helpers/fileNamer.helper';
import { Response } from 'express';
import { diskStorage } from 'multer';
import { Post, Param, Delete, UploadedFile, UseInterceptors, BadRequestException, Res } from '@nestjs/common';
  .
  .
  
  @Get(':product/:imageName')
  findProductImage(
    @Res() res: Response, // I can use Express.Response without the need of importing it.
    @Param('imageName') imageName: string
  ) {

    const path = this.fileService.getProductImage( imageName );

    /*return res.status(403).json({
      ok: false,
      path: path
    });*/
    res.send( path );
  }
  
  @Post('product')
  .
  .
    if( !file ) {
      throw new BadRequestException('Make sure that file is an image');
    }
    
    const secureUrl = `${ file.name }`;

    return { secureUrl }
  }

```

File service

```js
import { existsSync } from 'fs';
import { join } from 'path';
import { Injectable, BadRequestException } from '@nestjs/common';

@Injectable()
export class FileService {

// verifies if the image exists
  getStaticProductImage( imageName: string) {

    const path = join(__dirname, '../../static/products', imageName);

    if ( !existsSync(path) )
      throw new BadRequestException(`No product found with image ${ imageName }`);
    
    return path;

  }
}

```


<strong>Returning secureUrl</strong>

in the .env create a variable

```js
.
.

PORT=3000 
HOST_API=http://localhost:3000/api
```

In the files module import the <strong>ConfigModule</strong>.

```js
  .
  .
  imports:[
    ConfigModule
  ]

```

In the controller inject the ConfigService

```js


@Controller('files')
export class FilesController {
  constructor(
    private readonly fileService: FileService,
    private readonly configService: ConfigService
  )

  @Post('product')
  @UseInterceptors( FileInterceptor('file', {
    fileFilter: fileFilter,
    storage: diskStorage({
      destination: './static/products',
      filename: fileNamer
    })
  }))
  uploadProductImage(
    @uploadedFile() file: Express.Multer.File,
  ){
    if ( !file ) {
      throw new BadRequestException('Make sure that the file is an image');
    }

    const secureUrl = `${ this.configService.get('HOST_API')}/files/product/${ file.filename }`;

    return { secureUrl}
  }
}
```
