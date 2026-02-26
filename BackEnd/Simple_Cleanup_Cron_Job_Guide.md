# Data Cleanup Cron Job Implementation Guide

## 📋 Overview
This guide will help you implement an automatic data cleanup system that deletes records older than 30 days from your database. This prevents your database from growing indefinitely and helps manage storage costs.

## 🎯 What You'll Achieve
- Automatically delete records older than 30 days
- Run cleanup task daily (or at your preferred schedule)
- Monitor cleanup operations through logs
- Keep your database size under control

---

## 📚 Table of Contents
1. [Install Required Package](#step-1-install-required-package)
2. [Create Cleanup Service](#step-2-create-cleanup-service)
3. [Configure the Cron Job Schedule](#step-3-configure-the-cron-job-schedule)
4. [Register in App Module](#step-4-register-in-app-module)
5. [Add Environment Configuration](#step-5-add-environment-configuration)
6. [Testing Your Cron Job](#step-6-testing-your-cron-job)
7. [Deployment Instructions](#step-7-deployment-instructions)
8. [Monitoring and Troubleshooting](#step-8-monitoring-and-troubleshooting)

---

## Step 1: Install Required Package

### What is @nestjs/schedule?
This package allows you to schedule tasks (cron jobs) in your NestJS application. Think of it as an alarm clock for your code - it will run specific functions at specific times automatically.

### Installation Command
Open your terminal in the project directory:
```bash
cd src/production/audit-trail-prc-api
yarn add @nestjs/schedule
```

**OR if you prefer npm:**
```bash
npm install @nestjs/schedule
```

---

## Step 2: Create Cleanup Service

### Why Create a Separate Service?
A dedicated service keeps your code organized and makes it easy to maintain. All cleanup logic will be in one place.

### Create the File
Create a new file: `src/production/audit-trail-prc-api/app/audit/activity-log/activity-log-cleanup.service.ts`

### Complete Service Code with Comments

```typescript
import { Injectable, Logger } from '@nestjs/common';
import { Cron, CronExpression } from '@nestjs/schedule';
import { InjectRepository } from '@nestjs/typeorm';
import { LessThan, Repository } from 'typeorm';
import { ActivityLog } from './entity/activity-log';

/**
 * This service handles automatic cleanup of old activity logs
 * It runs scheduled tasks to delete records older than a specified number of days
 */
@Injectable()
export class ActivityLogCleanupService {
  // Logger helps us track what the cleanup job is doing
  private readonly logger = new Logger(ActivityLogCleanupService.name);
  
  // Number of days after which records should be deleted
  // You can change this value or make it configurable via environment variable
  private readonly RETENTION_DAYS = 30;

  constructor(
    // Inject the ActivityLog repository to perform database operations
    @InjectRepository(ActivityLog)
    private readonly activityLogRepository: Repository<ActivityLog>,
  ) {}

  /**
   * This method runs automatically based on the cron schedule
   * 
   * Cron Schedule Explained:
   * '0 2 * * *' means: Run at 2:00 AM every day
   * 
   * Format: second minute hour day month dayOfWeek
   * - 0 = at 0 seconds
   * - 2 = at 2 AM
   * - * = every day
   * - * = every month
   * - * = every day of week
   * 
   * Common Schedules:
   * - '0 0 * * *'     => Every day at midnight
   * - '0 2 * * *'     => Every day at 2 AM
   * - '0 0 * * 0'     => Every Sunday at midnight
   * - '0 */6 * * *'   => Every 6 hours
   * - '0 0 1 * *'     => First day of every month at midnight
   */
  @Cron('0 2 * * *', {
    name: 'cleanup-old-activity-logs',  // Give the job a name for monitoring
    timeZone: 'UTC',  // Set timezone (change to your deployment timezone)
  })
  async cleanupOldRecords() {
    // Log when the job starts
    this.logger.log('Starting cleanup job for old activity logs...');

    try {
      // Calculate the cutoff date (30 days ago from now)
      const cutoffDate = new Date();
      cutoffDate.setDate(cutoffDate.getDate() - this.RETENTION_DAYS);

      // Log the cutoff date for debugging
      this.logger.log(`Deleting records older than: ${cutoffDate.toISOString()}`);

      // Delete all records where createdOn is less than (older than) cutoffDate
      const result = await this.activityLogRepository.delete({
        createdOn: LessThan(cutoffDate),
      });

      // Log how many records were deleted
      this.logger.log(
        `Cleanup completed successfully. Deleted ${result.affected || 0} records older than ${this.RETENTION_DAYS} days.`
      );

      // Return result for testing purposes
      return {
        success: true,
        deletedCount: result.affected || 0,
        cutoffDate: cutoffDate.toISOString(),
      };
    } catch (error) {
      // If something goes wrong, log the error
      this.logger.error(
        `Failed to cleanup old activity logs: ${error.message}`,
        error.stack,
      );
      
      // Don't throw error - we don't want to crash the application
      return {
        success: false,
        error: error.message,
      };
    }
  }

  /**
   * Manual cleanup method - useful for testing or manual triggers
   * You can call this method from a controller or testing script
   * 
   * Example: POST /api/cleanup/activity-logs
   */
  async manualCleanup(daysOld: number = this.RETENTION_DAYS) {
    this.logger.log(`Manual cleanup triggered for records older than ${daysOld} days`);

    const cutoffDate = new Date();
    cutoffDate.setDate(cutoffDate.getDate() - daysOld);

    try {
      const result = await this.activityLogRepository.delete({
        createdOn: LessThan(cutoffDate),
      });

      return {
        success: true,
        deletedCount: result.affected || 0,
        cutoffDate: cutoffDate.toISOString(),
        retentionDays: daysOld,
      };
    } catch (error) {
      this.logger.error(`Manual cleanup failed: ${error.message}`, error.stack);
      throw error;
    }
  }

  /**
   * Get count of records that will be deleted in next cleanup
   * Useful for monitoring and alerting
   */
  async getRecordsToBeDeleted(): Promise<number> {
    const cutoffDate = new Date();
    cutoffDate.setDate(cutoffDate.getDate() - this.RETENTION_DAYS);

    const count = await this.activityLogRepository.count({
      where: {
        createdOn: LessThan(cutoffDate),
      },
    });

    this.logger.log(`Found ${count} records eligible for deletion`);
    return count;
  }

  /**
   * Get statistics about the cleanup operation
   * Returns information about old records and retention policy
   */
  async getCleanupStats() {
    const cutoffDate = new Date();
    cutoffDate.setDate(cutoffDate.getDate() - this.RETENTION_DAYS);

    const [oldRecordsCount, totalRecordsCount] = await Promise.all([
      this.activityLogRepository.count({
        where: {
          createdOn: LessThan(cutoffDate),
        },
      }),
      this.activityLogRepository.count(),
    ]);

    return {
      retentionDays: this.RETENTION_DAYS,
      cutoffDate: cutoffDate.toISOString(),
      recordsToBeDeleted: oldRecordsCount,
      totalRecords: totalRecordsCount,
      recordsToKeep: totalRecordsCount - oldRecordsCount,
    };
  }
}
```

---

## Step 3: Configure the Cron Job Schedule

### Understanding Cron Expressions

#### Visual Guide:
```
 ┌────────────── second (0-59)
 │ ┌──────────── minute (0-59)
 │ │ ┌────────── hour (0-23)
 │ │ │ ┌──────── day of month (1-31)
 │ │ │ │ ┌────── month (1-12)
 │ │ │ │ │ ┌──── day of week (0-7) (0 or 7 is Sunday)
 │ │ │ │ │ │
 * * * * * *
```

#### Common Examples:

```typescript
// Every day at 2:00 AM
@Cron('0 2 * * *')

// Every day at midnight
@Cron('0 0 * * *')

// Every Sunday at 3:00 AM
@Cron('0 3 * * 0')

// Every 6 hours
@Cron('0 */6 * * *')

// First day of every month at midnight
@Cron('0 0 1 * *')

// Every hour at minute 30
@Cron('0 30 * * * *')

// Every 15 minutes
@Cron('0 */15 * * * *')
```

#### Use NestJS Built-in Expressions:
```typescript
import { CronExpression } from '@nestjs/schedule';

@Cron(CronExpression.EVERY_DAY_AT_MIDNIGHT)    // 0 0 * * *
@Cron(CronExpression.EVERY_DAY_AT_1AM)         // 0 1 * * *
@Cron(CronExpression.EVERY_DAY_AT_2AM)         // 0 2 * * *
@Cron(CronExpression.EVERY_WEEK)               // 0 0 * * 0
@Cron(CronExpression.EVERY_WEEKDAY)            // 0 0 * * 1-5
@Cron(CronExpression.EVERY_WEEKEND)            // 0 0 * * 0,6
@Cron(CronExpression.EVERY_HOUR)               // 0 * * * *
@Cron(CronExpression.EVERY_30_MINUTES)         // */30 * * * *
```

### Choose Your Schedule
For data cleanup, **2:00 AM daily** is recommended (low traffic time):
```typescript
@Cron('0 2 * * *')
```

---

## Step 4: Register in App Module

### Why Register in Module?
NestJS needs to know about your service and the ScheduleModule to run cron jobs properly.

### Update activity-log.module.ts

**File Location:** `src/production/audit-trail-prc-api/app/audit/activity-log/activity-log.module.ts`

```typescript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { ActivityLog } from './entity/activity-log';
import { ActivityLogController } from './activity-log.controller';
import { ActivityLogService } from './activity-log.service';
import { ActivityLogMapperService } from './activity-log-mapper.service';

// Import the new cleanup service
import { ActivityLogCleanupService } from './activity-log-cleanup.service';

@Module({
  // Import TypeORM for database access
  imports: [TypeOrmModule.forFeature([ActivityLog])],
  
  // Register the cleanup service so NestJS can create and manage it
  providers: [
    ActivityLogService,
    ActivityLogMapperService,
    ActivityLogCleanupService,  // Add this line
  ],
  
  controllers: [ActivityLogController],
  exports: [ActivityLogService],
})
export class ActivityLogModule {}
```

### Update app.module.ts

**File Location:** `src/production/audit-trail-prc-api/app/app.module.ts`

```typescript
import { Logger, Module } from '@nestjs/common';
import { HttpModule } from '@nestjs/axios';
import { ConfigModule } from '@nestjs/config';
import { TypeOrmModule } from '@nestjs/typeorm';

// Import ScheduleModule - REQUIRED for cron jobs to work
import { ScheduleModule } from '@nestjs/schedule';

import { ActivityLogModule } from './audit/activity-log/activity-log.module';
import { GaurdsModule } from './audit/gaurds/gaurds.module';
import { DashboardVersion } from './version/dashboard_version.entity';
import { ActivityLog } from './audit/activity-log/entity/activity-log';
import AzureKvUtil from './audit/util/azure-kv-util';

Logger.log(`using profile: ${process.env.HABITAT} & config path ${__dirname}/audit-trail-prc-api-api/envs/.env.${process.env.HABITAT}`);

const azureKV = new AzureKvUtil();

@Module({
  imports: [
    ConfigModule.forRoot({
      envFilePath: `${__dirname}/audit-trail-prc-api-api/envs/.env.${process.env.HABITAT}`,
      isGlobal: true,
    }),
    
    // Add ScheduleModule - this enables cron job functionality
    ScheduleModule.forRoot(),
    
    TypeOrmModule.forRootAsync({
      useFactory: async () => ({
        type: 'postgres',
        host: process.env.DB_HOST,
        port: +process.env.DB_PORT,
        username: await azureKV.getDBUser(),
        password: await azureKV.getDBPassword(),
        database: process.env.DB_NAME,
        schema: process.env.DB_SCHEMA,
        entities: [DashboardVersion, ActivityLog],
        ssl: process.env.HABITAT.toUpperCase() !== 'LOCAL' && process.env.DB_isSSL_ENABLED === 'true',
        logging: true,
        synchronize: process.env.HABITAT.toUpperCase() === 'LOCAL',
        autoLoadEntities: true,
      }),
    }),
    HttpModule.register({
      timeout: 5000,
      maxRedirects: 5,
    }),
    ActivityLogModule,
    GaurdsModule,
  ],
})
export class AppModule {}
```

---

## Step 5: Add Environment Configuration

### Make Retention Days Configurable

Sometimes you want different retention policies for different environments (dev, staging, production).

### Update Your Environment Files

**File Location:** `src/production/audit-trail-prc-api/app/envs/.env.{HABITAT}`

Add this line to your environment files:
```bash
# Number of days to keep activity logs before automatic deletion
DATA_RETENTION_DAYS=30

# Enable/disable automatic cleanup (useful for testing)
ENABLE_AUTO_CLEANUP=true

# Cleanup schedule timezone
CLEANUP_TIMEZONE=UTC
```

### Update Cleanup Service to Use Environment Variables

Modify the cleanup service to read from environment:

```typescript
import { Injectable, Logger } from '@nestjs/common';
import { Cron } from '@nestjs/schedule';
import { ConfigService } from '@nestjs/config';
import { InjectRepository } from '@nestjs/typeorm';
import { LessThan, Repository } from 'typeorm';
import { ActivityLog } from './entity/activity-log';

@Injectable()
export class ActivityLogCleanupService {
  private readonly logger = new Logger(ActivityLogCleanupService.name);

  constructor(
    @InjectRepository(ActivityLog)
    private readonly activityLogRepository: Repository<ActivityLog>,
    // Inject ConfigService to read environment variables
    private readonly configService: ConfigService,
  ) {}

  // Read retention days from environment, default to 30 if not set
  private getRetentionDays(): number {
    return this.configService.get<number>('DATA_RETENTION_DAYS', 30);
  }

  // Check if auto cleanup is enabled
  private isAutoCleanupEnabled(): boolean {
    return this.configService.get<boolean>('ENABLE_AUTO_CLEANUP', true);
  }

  @Cron('0 2 * * *', {
    name: 'cleanup-old-activity-logs',
    timeZone: process.env.CLEANUP_TIMEZONE || 'UTC',
  })
  async cleanupOldRecords() {
    // Skip if auto cleanup is disabled
    if (!this.isAutoCleanupEnabled()) {
      this.logger.log('Auto cleanup is disabled via configuration');
      return { success: false, reason: 'Cleanup disabled' };
    }

    const retentionDays = this.getRetentionDays();
    this.logger.log(`Starting cleanup job for records older than ${retentionDays} days...`);

    try {
      const cutoffDate = new Date();
      cutoffDate.setDate(cutoffDate.getDate() - retentionDays);

      this.logger.log(`Deleting records older than: ${cutoffDate.toISOString()}`);

      const result = await this.activityLogRepository.delete({
        createdOn: LessThan(cutoffDate),
      });

      this.logger.log(
        `Cleanup completed. Deleted ${result.affected || 0} records older than ${retentionDays} days.`
      );

      return {
        success: true,
        deletedCount: result.affected || 0,
        cutoffDate: cutoffDate.toISOString(),
        retentionDays,
      };
    } catch (error) {
      this.logger.error(`Failed to cleanup: ${error.message}`, error.stack);
      return { success: false, error: error.message };
    }
  }
}
```

---

## Step 6: Testing Your Cron Job

### Option 1: Manual Testing Endpoint (Recommended)

Create a test controller to manually trigger cleanup:

**Create file:** `src/production/audit-trail-prc-api/app/audit/activity-log/cleanup-test.controller.ts`

```typescript
import { Controller, Post, Get, Query, UseGuards } from '@nestjs/common';
import { ActivityLogCleanupService } from './activity-log-cleanup.service';

/**
 * Controller for testing and manually triggering cleanup
 * IMPORTANT: In production, protect these endpoints with authentication
 */
@Controller('admin/cleanup')
// @UseGuards(AuthGuard) // Uncomment this in production
export class CleanupTestController {
  constructor(
    private readonly cleanupService: ActivityLogCleanupService,
  ) {}

  /**
   * Manually trigger cleanup
   * Example: POST http://localhost:3000/admin/cleanup/trigger?days=30
   */
  @Post('trigger')
  async triggerCleanup(@Query('days') days?: string) {
    const daysOld = days ? parseInt(days, 10) : 30;
    return await this.cleanupService.manualCleanup(daysOld);
  }

  /**
   * Get count of records that would be deleted
   * Example: GET http://localhost:3000/admin/cleanup/preview
   */
  @Get('preview')
  async previewCleanup() {
    const count = await this.cleanupService.getRecordsToBeDeleted();
    return {
      recordsToBeDeleted: count,
      message: `${count} records will be deleted in next cleanup`,
    };
  }

  /**
   * Get cleanup statistics
   * Example: GET http://localhost:3000/admin/cleanup/stats
   */
  @Get('stats')
  async getStats() {
    return await this.cleanupService.getCleanupStats();
  }
}
```

**Register in module:**
```typescript
// In activity-log.module.ts
import { CleanupTestController } from './cleanup-test.controller';

@Module({
  // ... other config
  controllers: [ActivityLogController, CleanupTestController],
})
```

### Option 2: Test with Short Interval

Temporarily change cron schedule for testing:

```typescript
// Change from daily to every minute for testing
@Cron('*/1 * * * *')  // Runs every minute
async cleanupOldRecords() {
  // ... cleanup code
}

// Don't forget to change it back after testing!
```

### Option 3: Unit Testing

**Create file:** `src/_tests_/unit/services/activity-log-cleanup.service.test.ts`

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { getRepositoryToken } from '@nestjs/typeorm';
import { ConfigService } from '@nestjs/config';
import { ActivityLogCleanupService } from '../../../production/audit-trail-prc-api/app/audit/activity-log/activity-log-cleanup.service';
import { ActivityLog } from '../../../production/audit-trail-prc-api/app/audit/activity-log/entity/activity-log';

describe('ActivityLogCleanupService', () => {
  let service: ActivityLogCleanupService;
  let mockRepository: any;
  let mockConfigService: any;

  beforeEach(async () => {
    // Create mock repository
    mockRepository = {
      delete: jest.fn(),
      count: jest.fn(),
    };

    // Create mock config service
    mockConfigService = {
      get: jest.fn((key: string, defaultValue: any) => {
        const config = {
          DATA_RETENTION_DAYS: 30,
          ENABLE_AUTO_CLEANUP: true,
        };
        return config[key] || defaultValue;
      }),
    };

    const module: TestingModule = await Test.createTestingModule({
      providers: [
        ActivityLogCleanupService,
        {
          provide: getRepositoryToken(ActivityLog),
          useValue: mockRepository,
        },
        {
          provide: ConfigService,
          useValue: mockConfigService,
        },
      ],
    }).compile();

    service = module.get<ActivityLogCleanupService>(ActivityLogCleanupService);
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });

  it('should delete old records successfully', async () => {
    // Mock the delete operation to return 50 affected records
    mockRepository.delete.mockResolvedValue({ affected: 50 });

    const result = await service.cleanupOldRecords();

    expect(result.success).toBe(true);
    expect(result.deletedCount).toBe(50);
    expect(mockRepository.delete).toHaveBeenCalled();
  });

  it('should handle errors gracefully', async () => {
    // Mock the delete operation to throw an error
    mockRepository.delete.mockRejectedValue(new Error('Database error'));

    const result = await service.cleanupOldRecords();

    expect(result.success).toBe(false);
    expect(result.error).toBe('Database error');
  });

  it('should get cleanup stats correctly', async () => {
    // Mock count operations
    mockRepository.count.mockResolvedValueOnce(100) // old records
                         .mockResolvedValueOnce(500); // total records

    const stats = await service.getCleanupStats();

    expect(stats.recordsToBeDeleted).toBe(100);
    expect(stats.totalRecords).toBe(500);
    expect(stats.recordsToKeep).toBe(400);
  });
});
```

**Run the test:**
```bash
yarn test --t ActivityLogCleanupService
```

---

## Step 7: Deployment Instructions

### Checklist Before Deployment

- [ ] Install @nestjs/schedule package
- [ ] Create ActivityLogCleanupService
- [ ] Register service in ActivityLogModule
- [ ] Add ScheduleModule to AppModule
- [ ] Set environment variables
- [ ] Test cleanup locally
- [ ] Review cron schedule timing
- [ ] Update package.json if needed

### Deployment Steps

#### For Docker Deployment:

1. **Rebuild your Docker image:**
```bash
docker build -t audit-trail-prc-api:latest .
```

2. **Update docker-compose.yml** to include environment variables:
```yaml
services:
  api:
    image: audit-trail-prc-api:latest
    environment:
      - DATA_RETENTION_DAYS=30
      - ENABLE_AUTO_CLEANUP=true
      - CLEANUP_TIMEZONE=UTC
```

3. **Deploy the updated container:**
```bash
docker-compose up -d
```

#### For Kubernetes/AKS Deployment:

1. **Update your deployment YAML:**
```yaml
# aks-deployment.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: audit-trail-prc-api
spec:
  template:
    spec:
      containers:
      - name: api
        image: your-registry/audit-trail-prc-api:latest
        env:
        - name: DATA_RETENTION_DAYS
          value: "30"
        - name: ENABLE_AUTO_CLEANUP
          value: "true"
        - name: CLEANUP_TIMEZONE
          value: "UTC"
```

2. **Apply the updated deployment:**
```bash
kubectl apply -f deploy/application/content/aks-deployment.yml
```

#### For OpenShift Deployment:

Similar to Kubernetes, update your openshift-deployment.yml with the environment variables.

### Post-Deployment Verification

1. **Check application logs:**
```bash
# Docker
docker logs -f <container-id>

# Kubernetes
kubectl logs -f <pod-name>
```

2. **Look for these log messages:**
```
[ActivityLogCleanupService] Starting cleanup job for old activity logs...
[ActivityLogCleanupService] Cleanup completed. Deleted X records older than 30 days.
```

3. **Monitor first scheduled run:**
   - Wait for the scheduled time (e.g., 2:00 AM)
   - Check logs the next morning
   - Verify records were deleted

---

## Step 8: Monitoring and Troubleshooting

### Monitoring Cleanup Operations

#### Check Logs in Production

The cleanup service logs important information:

```typescript
// Logs you'll see:
[ActivityLogCleanupService] Starting cleanup job for records older than 30 days...
[ActivityLogCleanupService] Deleting records older than: 2026-01-21T02:00:00.000Z
[ActivityLogCleanupService] Cleanup completed. Deleted 1523 records older than 30 days.
```

#### Create Monitoring Dashboard

If you use monitoring tools (Grafana, DataDog, etc.), track:
- Number of records deleted per cleanup
- Cleanup execution time
- Failed cleanup attempts
- Database size over time

### Common Issues and Solutions

#### Issue 1: Cron Job Not Running

**Symptoms:** No cleanup logs appear, records not being deleted

**Solutions:**
```typescript
// 1. Check if ScheduleModule is registered in app.module.ts
import { ScheduleModule } from '@nestjs/schedule';

@Module({
  imports: [
    ScheduleModule.forRoot(), // This line MUST be present
    // ... other imports
  ],
})

// 2. Check if cleanup service is registered in providers
@Module({
  providers: [
    ActivityLogCleanupService, // This line MUST be present
    // ... other providers
  ],
})

// 3. Verify @nestjs/schedule is installed
// Run: yarn list @nestjs/schedule
```

#### Issue 2: Timezone Issues

**Symptoms:** Cron runs at wrong time

**Solutions:**
```typescript
// Specify timezone explicitly
@Cron('0 2 * * *', {
  timeZone: 'America/New_York',  // Use your deployment region
})

// Common timezones:
// - 'UTC'
// - 'America/New_York'
// - 'Europe/London'
// - 'Asia/Shanghai'
// - 'Asia/Kolkata'
```

#### Issue 3: Database Connection Errors

**Symptoms:** Cleanup logs show errors, records not deleted

**Solutions:**
```typescript
// Add retry logic
async cleanupOldRecords() {
  const maxRetries = 3;
  let attempt = 0;

  while (attempt < maxRetries) {
    try {
      // ... cleanup logic
      return result;
    } catch (error) {
      attempt++;
      this.logger.warn(`Cleanup attempt ${attempt} failed: ${error.message}`);
      
      if (attempt < maxRetries) {
        // Wait before retrying (exponential backoff)
        await new Promise(resolve => setTimeout(resolve, 1000 * attempt));
      } else {
        this.logger.error('Cleanup failed after max retries');
        throw error;
      }
    }
  }
}
```

#### Issue 4: Too Many Records to Delete

**Symptoms:** Cleanup takes too long, database locks

**Solutions:**
```typescript
// Delete in batches instead of all at once
async cleanupOldRecords() {
  const BATCH_SIZE = 1000; // Delete 1000 records at a time
  let totalDeleted = 0;

  while (true) {
    try {
      const cutoffDate = new Date();
      cutoffDate.setDate(cutoffDate.getDate() - this.RETENTION_DAYS);

      // Find records to delete (with limit)
      const recordsToDelete = await this.activityLogRepository.find({
        where: { createdOn: LessThan(cutoffDate) },
        take: BATCH_SIZE,
        select: ['id'], // Only fetch IDs for efficiency
      });

      if (recordsToDelete.length === 0) {
        break; // No more records to delete
      }

      // Delete this batch
      const ids = recordsToDelete.map(r => r.id);
      await this.activityLogRepository.delete(ids);
      
      totalDeleted += recordsToDelete.length;
      this.logger.log(`Deleted batch of ${recordsToDelete.length} records`);

      // Small delay between batches to avoid overwhelming database
      await new Promise(resolve => setTimeout(resolve, 100));

    } catch (error) {
      this.logger.error(`Batch deletion failed: ${error.message}`);
      break;
    }
  }

  this.logger.log(`Total deleted: ${totalDeleted} records`);
  return { success: true, deletedCount: totalDeleted };
}
```

### Performance Optimization

#### Add Database Index

For faster cleanup, add an index on the `createdOn` column:

```sql
-- Run this in your PostgreSQL database
CREATE INDEX IF NOT EXISTS idx_activity_log_created_on 
ON bafd.activity_log (createdOn);
```

Or using TypeORM migration:

```typescript
// In your entity file
@Entity('activity_log', { schema: 'bafd' })
@Index('idx_activity_log_created_on', ['createdOn'])
export class ActivityLog {
  // ... entity fields
}
```

---

## 📊 Advanced Features

### 1. Add Cleanup Notifications

Send alerts when cleanup completes or fails:

```typescript
import { Injectable, Logger } from '@nestjs/common';
import { HttpService } from '@nestjs/axios';

@Injectable()
export class ActivityLogCleanupService {
  constructor(
    private readonly httpService: HttpService,
    // ... other dependencies
  ) {}

  async cleanupOldRecords() {
    try {
      const result = await this.performCleanup();
      
      // Send success notification
      await this.sendNotification({
        status: 'success',
        deletedCount: result.deletedCount,
        message: `Successfully deleted ${result.deletedCount} old records`,
      });
      
      return result;
    } catch (error) {
      // Send failure notification
      await this.sendNotification({
        status: 'error',
        message: `Cleanup failed: ${error.message}`,
      });
      
      throw error;
    }
  }

  private async sendNotification(data: any) {
    try {
      // Send to Slack
      await this.httpService.post(process.env.SLACK_WEBHOOK_URL, {
        text: `[Audit Trail Cleanup] ${data.message}`,
        ...data,
      }).toPromise();
    } catch (error) {
      this.logger.warn('Failed to send notification', error);
    }
  }
}
```

### 2. Soft Delete Instead of Hard Delete

Keep records but mark them as deleted:

```typescript
// Add a new column to entity
@Column({ name: 'is_deleted', default: false })
isDeleted: boolean;

@Column({ name: 'deleted_at', nullable: true })
deletedAt: Date;

// Update cleanup to soft delete
async cleanupOldRecords() {
  const cutoffDate = new Date();
  cutoffDate.setDate(cutoffDate.getDate() - this.RETENTION_DAYS);

  const result = await this.activityLogRepository.update(
    {
      createdOn: LessThan(cutoffDate),
      isDeleted: false,
    },
    {
      isDeleted: true,
      deletedAt: new Date(),
    }
  );

  this.logger.log(`Soft deleted ${result.affected} records`);
}

// Later, permanently delete soft-deleted records after 90 days
async permanentlyDeleteOldRecords() {
  const cutoffDate = new Date();
  cutoffDate.setDate(cutoffDate.getDate() - 90);

  const result = await this.activityLogRepository.delete({
    isDeleted: true,
    deletedAt: LessThan(cutoffDate),
  });

  this.logger.log(`Permanently deleted ${result.affected} records`);
}
```

### 3. Archive Old Data Before Deletion

Move old data to archive table before deleting:

```typescript
async cleanupOldRecords() {
  const cutoffDate = new Date();
  cutoffDate.setDate(cutoffDate.getDate() - this.RETENTION_DAYS);

  // Find records to archive
  const recordsToArchive = await this.activityLogRepository.find({
    where: { createdOn: LessThan(cutoffDate) },
  });

  if (recordsToArchive.length > 0) {
    // Insert into archive table
    await this.archiveRepository.save(recordsToArchive);
    
    // Delete from main table
    const ids = recordsToArchive.map(r => r.id);
    await this.activityLogRepository.delete(ids);
    
    this.logger.log(`Archived and deleted ${recordsToArchive.length} records`);
  }
}
```

---

## 🎓 Summary

### What You've Accomplished

✅ Installed scheduling package
✅ Created automatic cleanup service
✅ Configured cron job schedule
✅ Added environment variables for flexibility
✅ Implemented monitoring and logging
✅ Created testing endpoints
✅ Prepared for deployment

### Key Takeaways

1. **Cron jobs run automatically** - No manual intervention needed
2. **Configurable retention period** - Easy to adjust via environment variables
3. **Safe error handling** - Won't crash your application
4. **Comprehensive logging** - Easy to monitor and debug
5. **Production-ready** - Includes testing and monitoring features

### Next Steps

1. ✅ Deploy to your environment
2. ✅ Monitor first cleanup run
3. ✅ Set up alerts/notifications
4. ✅ Review and adjust retention period if needed
5. ✅ Consider adding cleanup stats dashboard

---

## 📞 Need Help?

### Common Questions

**Q: Will this delete data immediately when deployed?**
A: No, it will only delete data when the cron schedule runs (e.g., 2 AM daily).

**Q: Can I test it without waiting for the scheduled time?**
A: Yes, use the manual trigger endpoint: `POST /admin/cleanup/trigger`

**Q: What if I want to keep data for 60 days instead of 30?**
A: Update the `DATA_RETENTION_DAYS` environment variable to 60.

**Q: Will this affect application performance?**
A: No, cleanup runs during low-traffic hours and is designed to be non-blocking.

**Q: Can I disable cleanup temporarily?**
A: Yes, set `ENABLE_AUTO_CLEANUP=false` in environment variables.

### Useful Commands

```bash
# Install dependencies
yarn add @nestjs/schedule

# Run tests
yarn test --t ActivityLogCleanupService

# Check logs (Docker)
docker logs -f <container-id>

# Check logs (Kubernetes)
kubectl logs -f <pod-name>

# Manually trigger cleanup (curl)
curl -X POST http://localhost:3000/admin/cleanup/trigger

# Get cleanup stats
curl http://localhost:3000/admin/cleanup/stats
```

---

## 📝 Checklist for Implementation

Print this checklist and mark off each step:

```
[�] Step 1: Install @nestjs/schedule package
[ ] Step 2: Create activity-log-cleanup.service.ts
[ ] Step 3: Choose and configure cron schedule
[ ] Step 4: Register service in modules
[ ] Step 5: Add environment variables
[ ] Step 6: Create test endpoints
[ ] Step 7: Test locally
[ ] Step 8: Deploy to staging
[ ] Step 9: Monitor first cleanup
[ ] Step 10: Deploy to production
```

---

**Document Version:** 1.0
**Last Updated:** February 20, 2026
**Compatible With:** NestJS 9+, TypeORM 0.3+, Node.js 16+

---

Happy Coding! 🚀 Your database will thank you for keeping it clean and optimized!
