# Chapter 12 Emergence

## Runs All the Tests  ##

- Systyem should be testable and verifiable
- Better design(SRP, DIP) leads to easy testable system and vice versa

## Refactoring ##

- Prerequisite: testable system
- Goal: increase cohesion, decrease coupling, separate concerns, modularize system concers etc..


## No Duplication ##
> Duplication of implementation

```java
  public void scaleToOneDimension(float desiredDimension, float imageDimension) {
    
    if (Math.abs(desiredDimension - imageDimension) < errorThreshold) 
      return;
    
    float scalingFactor = desiredDimension / imageDimension; 
    scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);
    RenderedOp newImage = ImageUtilities.getScaledImage( image, scalingFactor, scalingFactor);
    image.dispose(); 
    System.gc(); 
    image = newImage;
  }

  public synchronized void rotate(int degrees) {
    RenderedOp newImage = ImageUtilities.getRotatedImage( image, degrees);
    image.dispose(); System.gc(); image = newImage;
  }

/////////////////////////////////////////////////////////////////////////////////////

  public void scaleToOneDimension(float desiredDimension, float imageDimension) {
    if (Math.abs(desiredDimension - imageDimension) < errorThreshold) return;
    float scalingFactor = desiredDimension / imageDimension; 
    scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);

    replaceImage(ImageUtilities.getScaledImage( image, scalingFactor, scalingFactor));
  }

  public synchronized void rotate(int degrees) {
    replaceImage(ImageUtilities.getRotatedImage(image, degrees));
  }

  private void replaceImage(RenderedOp newImage) { i
    mage.dispose();
    System.gc();
    image = newImage;
  }

```

> Template Design

```java
abstract public class VacationPolicy { 
  public void accrueVacation() {
    calculateBaseVacationHours();
    alterForLegalMinimums();
    applyToPayroll();
  }

  private void calculateBaseVacationHours() { /* ... */ }; 
  abstract protected void alterForLegalMinimums(); 
  private void applyToPayroll() { /* ... */ };
}


public class USVacationPolicy extends VacationPolicy { 
  @Override protected void alterForLegalMinimums() {
    // US specific logic 
  }
}

public class EUVacationPolicy extends VacationPolicy { 
  @Override protected void alterForLegalMinimums() {
    // EU specific logic 
  }
}
```

- No duplication of implementation
- Template method (OOP)


## Expressive ##

- Code should have readibility, for next person to get to understand easily
- Good naming, standard desing pattern, small function etc
