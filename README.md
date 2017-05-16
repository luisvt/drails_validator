# drails_validator

[![Build Status](https://travis-ci.org/drails-dart/drails_validator.svg)](https://travis-ci.org/drails-dart/drails_validator.svg)

Library for validate models similar to JPA Bean validation. It provides a way to validate a dart object using constraints which we can use for validation.
 
To check if the object is valid we need to invoke the function `validate`. This one returns an Object of type `ValidationResult` which contains a map of errors for every attribute of the object, for example:

1. Create a new dart project

2. Add dependencies to `pubspec.yaml`

```yaml
...
dependencies:
  ...
  drails_validator: ^0.1.0 #change it for the latest version
  ...
```

3.Create/edit the file `main.dart` in the folder `bin` and put next code on it:

```dart
// Copyright (c) 2015, Luis Vargas. All rights reserved. Use of this source code
// is governed by a BSD-style license that can be found in the LICENSE file.

library Validator.example;

import 'package:validator/validator.dart' as validator;
import 'package:drails_validator/drails_validator.dart';

part 'drails_validator_sample.g.dart';

bool lowerThanOrEqualNow(DateTime dateOfBirth) =>
  !dateOfBirth.isAfter(new DateTime.now());

bool isSSN(String ssn) =>
  validator.matches(ssn, r'\d\d\d-\d\d-\d\d\d\d');

bool isEmail(String email) =>
    isNotNull(email) && validator.isEmail(email);

@serializable
class Person extends _$PersonSerializable {
  int id;
  
  @Length(min: 2)
  String firstName;
  
  @Length(min: 2)
  String lastName;
  
  @ValidIf(isEmail, customDescription: 'The entered email is invalid')
  String email;
  
  @ValidIf(lowerThanOrEqualNow, customDescription: 'Values after now are not allowed')
  DateTime dateOfBirth;
  
  @ValidIf(isSSN, customDescription: 'The entered SSN is invalid')
  String ssn;
}

main() {
  _initMirrors();

  var invalidPerson = new Person()
        ..id = 1
        ..firstName = ''
        ..lastName = ''
        ..dateOfBirth = new DateTime(2030, 11, 9)
        ..ssn = '123',
      validPerson = new Person()
        ..id = 2
        ..firstName = 'Joe'
        ..lastName = 'Doe'
        ..email = 'joedoe@email.com'
        ..dateOfBirth = new DateTime(1989, 11, 9)
        ..ssn = '123-45-6789';

  print('invalidPerson: ${validate(invalidPerson)}');
  print('validPerson: ${validate(validPerson)}');
}
  
```

5. create a file in `tool` folder called `build.dart` and put next code on it:

```dart
import 'package:build_runner/build_runner.dart';
import 'package:dson/phase.dart';


main() async {
  await build(new PhaseGroup()
    ..addPhase(
      // In next line replace `example/**.dart` for the globs you want to use as input, for example `**/*.dart`
      // to take all the dart files of the project as input.
        dsonPhase(const ['example/**.dart'])),
      deleteFilesByDefault: true);
}
```

6. run `tool/build.dart`. Then you will see that the file `bin/drails_validator_sample.g.dart`
has been generated and it will contains the next code:

```dart
// GENERATED CODE - DO NOT MODIFY BY HAND

part of Validator.example;

// **************************************************************************
// Generator: InitMirrorsGenerator
// Target: library Validator.example
// **************************************************************************

_initMirrors() {
  initClassMirrors({Person: PersonClassMirror});
  initFunctionMirrors({});
}

// **************************************************************************
// Generator: DsonGenerator
// Target: class Person
// **************************************************************************

abstract class _$PersonSerializable extends SerializableMap {
  int get id;
  String get firstName;
  String get lastName;
  String get email;
  DateTime get dateOfBirth;
  String get ssn;
  void set id(int v);
  void set firstName(String v);
  void set lastName(String v);
  void set email(String v);
  void set dateOfBirth(DateTime v);
  void set ssn(String v);

  operator [](Object key) {
    switch (key) {
      case 'id':
        return id;
      case 'firstName':
        return firstName;
      case 'lastName':
        return lastName;
      case 'email':
        return email;
      case 'dateOfBirth':
        return dateOfBirth;
      case 'ssn':
        return ssn;
    }
    throwFieldNotFoundException(key, 'Person');
  }

  operator []=(Object key, value) {
    switch (key) {
      case 'id':
        id = value;
        return;
      case 'firstName':
        firstName = value;
        return;
      case 'lastName':
        lastName = value;
        return;
      case 'email':
        email = value;
        return;
      case 'dateOfBirth':
        dateOfBirth = value;
        return;
      case 'ssn':
        ssn = value;
        return;
    }
    throwFieldNotFoundException(key, 'Person');
  }

  get keys => PersonClassMirror.fields.keys;
}

_Person__Constructor(params) => new Person();

const $$Person_fields_id = const DeclarationMirror(type: int);
const $$Person_fields_firstName = const DeclarationMirror(
    type: String,
    annotations: const [
      const Length(min: 2, max: null, customDescription: null)
    ]);
const $$Person_fields_lastName = const DeclarationMirror(
    type: String,
    annotations: const [
      const Length(min: 2, max: null, customDescription: null)
    ]);
const $$Person_fields_email = const DeclarationMirror(
    type: String,
    annotations: const [
      const ValidIf(isEmail, customDescription: r'The entered email is invalid')
    ]);
const $$Person_fields_dateOfBirth =
    const DeclarationMirror(type: DateTime, annotations: const [
  const ValidIf(lowerThanOrEqualNow,
      customDescription: r'Values after now are not allowed')
]);
const $$Person_fields_ssn = const DeclarationMirror(
    type: String,
    annotations: const [
      const ValidIf(isSSN, customDescription: r'The entered SSN is invalid')
    ]);

const PersonClassMirror =
    const ClassMirror(name: 'Person', constructors: const {
  '': const FunctionMirror(parameters: const {}, call: _Person__Constructor)
}, fields: const {
  'id': $$Person_fields_id,
  'firstName': $$Person_fields_firstName,
  'lastName': $$Person_fields_lastName,
  'email': $$Person_fields_email,
  'dateOfBirth': $$Person_fields_dateOfBirth,
  'ssn': $$Person_fields_ssn
}, getters: const [
  'id',
  'firstName',
  'lastName',
  'email',
  'dateOfBirth',
  'ssn'
], setters: const [
  'id',
  'firstName',
  'lastName',
  'email',
  'dateOfBirth',
  'ssn'
]);

```

7. Finally you can run the file `bin/drails_validator_sample.dart`. If everything is ok you will see next
output in console:

```
invalidPerson: isValid: false, errors: {firstName: [Length should be greather than 2], lastName: [Length should be greather than 2], email: [The entered email is invalid], dateOfBirth: [Values after now are not allowed], ssn: [The entered SSN is invalid]}
validPerson: isValid: true, errors: {}
```